---
title: HDInsight 'ta Apache Hadoop MapReduce örnekleri çalıştırma-Azure
description: HDInsight 'ta bulunan jar dosyalarında MapReduce örneklerini kullanmaya başlayın. Kümeye bağlanmak için SSH kullanın ve ardından örnek işleri çalıştırmak için Hadoop komutunu kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943210"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>HDInsight 'ta bulunan MapReduce örneklerini çalıştırma

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

HDInsight üzerinde Apache Hadoop eklenen MapReduce örneklerini çalıştırmayı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>MapReduce örnekleri

Örnekler, adresindeki HDInsight kümesinde bulunur `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` . Bu örnekler için kaynak kodu, adresindeki HDInsight kümesinde bulunur `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` .

Aşağıdaki örnekler bu arşivde yer alır:

|Örnek |Açıklama |
|---|---|
|aggregatewordcount|Giriş dosyalarındaki sözcükleri sayar.|
|aggregatewordhist|Giriş dosyalarındaki sözcüklerin histogramını hesaplar.|
|barp|Pi 'nin tam rakamlarını hesaplamak için Bailey-Borwein-Plouffe kullanır.|
|dbcount|Veritabanında depolanan sayfa görüntülemesi günlüklerini sayar.|
|distbbp|Tam Pi bitlerini hesaplamak için BBP türünde bir formül kullanır.|
|grep|Girişte bir Regex ile eşleşen eşleşmeleri sayar.|
|join|Sıralanmış, eşit olarak bölümlenmiş veri kümeleri üzerinde bir JOIN gerçekleştirir.|
|mulalliwc|Birkaç dosyadan sözcükleri sayar.|
|pentomino dili|Pentomino sorunlarına çözüm bulmak için kutucuk yerleştirme programı.|
|PI|Bir dörde-Monte Carlo yöntemi kullanarak Pi tahminleri yapın.|
|rasgeletextwriter|Düğüm başına 10 GB 'lık rastgele metin verisi yazar.|
|Rastgele yazıcı|Düğüm başına 10 GB rastgele veri yazar.|
|ikincil sıralama|Azaltma aşamasında ikinci sıralamayı tanımlar.|
|sort|Rastgele yazıcı tarafından yazılan verileri sıralar.|
|sudoku|Bir sudoku çözücü.|
|teragen|Terasort için veri oluşturma.|
|terasort|Terasort çalıştırın.|
|teravalidate|Terasort sonuçları denetleniyor.|
|WORDCOUNT|Giriş dosyalarındaki sözcükleri sayar.|
|wordortalama|Giriş dosyalarındaki sözcüklerin ortalama uzunluğunu sayar.|
|wordortanca|Giriş dosyalarındaki sözcüklerin ortanca uzunluğunu sayar.|
|wordstandardsapması|Giriş dosyalarındaki sözcüklerin uzunluğunun standart sapmasını sayar.|

## <a name="run-the-wordcount-example"></a>WORDCOUNT örneğini çalıştırma

1. SSH kullanarak HDInsight 'a bağlanın. `CLUSTER`Kümenizin adıyla değiştirin ve aşağıdaki komutu girin:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. SSH oturumunda, örnekleri listelemek için aşağıdaki komutu kullanın:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Bu komut, bu belgenin önceki bölümündeki örnek listesini oluşturur.

3. Belirli bir örnek hakkında yardım almak için aşağıdaki komutu kullanın. Bu durumda, **WORDCOUNT** örneği:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Şu iletiyi alırsınız:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Bu ileti, kaynak belgeler için birkaç giriş yolu sağlayabileceğini belirtir. Son yol, çıktının (kaynak belgelerindeki sözcüklerin sayısı) depolandığı yerdir.

4. Kümenizle örnek veriler olarak sunulan Leonardo da Vinci Not defterlerindeki tüm sözcükleri saymak için aşağıdakileri kullanın:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Bu işin girişi öğesinden okundu `/example/data/gutenberg/davinci.txt` . Bu örneğin çıktısı içinde depolanır `/example/data/davinciwordcount` . Her iki yol da yerel dosya sistemi değil, küme için varsayılan depolamada bulunur.

   > [!NOTE]  
   > WORDCOUNT örneği için yardım 'da belirtildiği gibi, birden çok giriş dosyası da belirtebilirsiniz. Örneğin, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` hem davinci.txt hem de ulysses.txt sözcükleri sayar.

5. İş tamamlandıktan sonra çıktıyı görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Bu komut, iş tarafından üretilen tüm çıktı dosyalarını birleştirir. Çıktıyı konsola görüntüler. Çıktı aşağıdaki metne benzer:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Her satır bir kelimeyi ve girdi verilerinde kaç kez gerçekleştiğini temsil eder.

## <a name="the-sudoku-example"></a>Sudoku örneği

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) , dokuz 3x3 ızgaradan oluşan bir Logic bulmaca. Kılavuzdaki bazı hücrelerde sayı bulunur, diğerleri boş kalır ve hedef hücre için çözülmektedir. Önceki bağlantı, bulmaca hakkında daha fazla bilgi içerir, ancak bu örneğin amacı boş hücreleri çözmelidir. Bu nedenle, girimiz aşağıdaki biçimde bir dosya olmalıdır:

* Dokuz sütundan oluşan dokuz satır
* Her sütun bir sayı ya da `?` (boş bir hücreyi gösterir) içerebilir
* Hücreler boşlukla ayrılır

Sudoku bulmaca vs oluşturmak için belirli bir yol vardır. bir sütun veya satırdaki bir sayıyı yineleyemiyorum. HDInsight kümesinde düzgün şekilde oluşturulan bir örnek vardır. Konumunda bulunur `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` ve şu metni içerir:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Bu örnek sorunu Sudoku örneği aracılığıyla çalıştırmak için aşağıdaki komutu kullanın:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Sonuçlar aşağıdaki metne benzer şekilde görünür:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>PI (π) örneği

PI örneği, Pi değerini tahmin etmek için istatistiksel (quası-Monte Carlo) yöntemini kullanır. Noktaları, bir birim karede rastgele yerleştirilir. Kare de bir daire içerir. Noktaların daire içinde düşme olasılığı, Pi/4 çemberin alanına eşittir. PI değeri 4R değerinden tahmin edilebilir. R, daire içinde olan noktaların sayısının kare içinde olan toplam punto sayısına oranıdır. Kullanım noktalarının daha büyük olması, tahminin ne kadar iyi olduğunu gösterir.

Bu örneği çalıştırmak için aşağıdaki komutu kullanın. Bu komut, Pi değerini tahmin etmek için her biri 10.000.000 örnek ile 16 Haritalar kullanır:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Bu komut tarafından döndürülen değer **3.14159155000000000000** benzerdir. Başvurular için, Pi 'nin ilk 10 ondalık basamak 3,1415926535 ' dir.

## <a name="10-gb-graysort-example"></a>10 GB gri sıralama örneği

Gri tonlamalı bir kıyaslama sıralaması. Ölçüm, büyük miktarlarda verileri sıralarken elde edilen sıralama oranıdır (TB/dakika), genellikle en az 100 TB 'tır.

Bu örnek, görece hızlı bir şekilde çalıştırılabilmesi için bir ila büyüklükteki 10 GB veri kullanır. Owen 'Malley tarafından geliştirilen MapReduce uygulamalarını ve Arun Muralmi kullanır. Bu uygulamalar yıllık genel amaçlı ("Daytona") terabaytlık 2009 ' de 0,578, TB/dak oranında sıralama kıyaslaması (100 TB, 173 dakika) kazandı. Bu ve diğer sıralama kıyaslamaları hakkında daha fazla bilgi için bkz. [kıyaslama sitesini sıralama](https://sortbenchmark.org/) .

Bu örnek üç MapReduce programı kümesini kullanır:

* **Teragen**: sıralanacak veri satırları üreten MapReduce programı

* **TeraSort**: giriş verilerini örnekler ve verileri toplam bir sıraya göre sıralamak Için MapReduce kullanır

    TeraSort, özel bir bölümleyici hariç standart bir MapReduce sırsıdır. Bölümleyici, her bir küçültme için anahtar aralığını tanımlayan N-1 örneklenmiş anahtarların sıralanmış bir listesini kullanır. Özellikle, örnek [i-1] <= anahtar < örneği [i] gibi tüm anahtarlar ı azaltmak için gönderilir. Bu bölümleyici, ı azaltma çıktılarının t + 1 ' i düşürenden daha az olduğunu garanti eder.

* **TeraValidate**: çıktının genel olarak sıralanacağını doğrulayan bir MapReduce programı

    Çıktı dizininde dosya başına bir harita oluşturur ve her bir eşleme, her bir anahtarın öncekine eşit veya ondan küçük olmasını sağlar. Map işlevi her bir dosyanın ilk ve son anahtarlarının kayıtlarını oluşturur. Küçültme işlevi, dosya ı-1 ' in son anahtarından daha büyük olduğumu ilk anahtarının daha büyük olmasını sağlar. Tüm sorunlar, azalmış olan anahtarlarla birlikte, azaltma aşamasının bir çıkışı olarak bildirilir.

Veri oluşturmak, sıralamak ve sonra çıktıyı doğrulamak için aşağıdaki adımları kullanın:

1. HDInsight kümesinin varsayılan depolama alanında depolanan 10 GB veri oluşturun `/example/data/10GB-sort-input` :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks`Bu iş için kaç tane eşleme görevinin kullanılacağını Hadoop 'a söyler. Son iki parametre, işe 10 GB veri oluşturulmasını ve üzerinde depolamayı yönlendirir `/example/data/10GB-sort-input` .

2. Verileri sıralamak için aşağıdaki komutu kullanın:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    , `-Dmapred.reduce.tasks` İş için kaç tane ne kadar görev kullanacağınızı Hadoop 'a söyler. Son iki parametre yalnızca veri için giriş ve çıkış konumlarıdır.

3. Sıralama tarafından oluşturulan verileri doğrulamak için aşağıdakileri kullanın:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Linux tabanlı HDInsight kümelerine dahil edilen örneklerin nasıl çalıştırılacağını öğrendiniz. HDInsight ile Pig, Hive ve MapReduce kullanma hakkında öğreticiler için aşağıdaki konulara bakın:

* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)
* [HDInsight üzerinde Apache Hadoop MapReduce kullanma](hdinsight-use-mapreduce.md)
