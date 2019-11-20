---
title: Azure HDInsight 'ta Giraph 'yi yükleyip kullanma
description: Betik eylemleri kullanarak HDInsight kümelerine Giraph 'yi yüklemeyi öğrenin. Giraph 'yi kullanarak Azure bulutundaki Apache Hadoop grafik işleme gerçekleştirebilirsiniz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f1ca536ffa2166df4ef6cf51654b7b410e72ea66
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962047"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>HDInsight Hadoop kümelerine Apache Giraph 'yi yükler ve büyük ölçekli grafikleri işlemek için Giraph kullanma

Bir HDInsight kümesine Apache Giraph 'yi yüklemeyi öğrenin. HDInsight 'ın betik eylemi özelliği, bir bash betiği çalıştırarak kümenizi özelleştirmenizi sağlar. Betikler, küme oluşturma sırasında ve sonrasında kümeleri özelleştirmek için kullanılabilir.

## <a name="whatis"></a>Giraph nedir?

[Apache Giraph](https://giraph.apache.org/) , Hadoop kullanarak grafik işleme gerçekleştirmenize olanak tanır ve Azure HDInsight ile kullanılabilir. Nesneler arasındaki model ilişkilerini grafik olarak tanımlar. Örneğin, Internet gibi büyük bir ağdaki yönlendiriciler veya sosyal ağlardaki insanlar arasındaki ilişkiler arasındaki bağlantılar. Grafik işleme, bir grafikteki nesneler arasındaki ilişkilerle ilgili nedenleri sağlar; örneğin:

* Mevcut ilişkilerinizi temel alarak olası arkadaşları tanımlama.

* Bir ağdaki iki bilgisayar arasındaki en kısa yolu tanımlama.

* Web sayfalarının sayfa sırasını hesaplama.

> [!WARNING]  
> HDInsight kümesiyle birlikte sunulan bileşenler tam olarak desteklenir-Microsoft Desteği bu bileşenlerle ilgili sorunları yalıtmaya ve çözmeye yardımcı olur.
>
> Giraph gibi özel bileşenler, sorunu gidermeye yardımcı olmak için ticari olarak makul destek alır. Microsoft Desteği sorunu çözebiliyor olabilir. Aksi takdirde, bu teknolojinin derin uzmanlığı bulunan açık kaynaklı topluluklara başvurmalısınız. Örneğin, şu şekilde kullanılabilecek birçok topluluk sitesi vardır: [HDInsight Için MSDN Forumu](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Ayrıca Apache projelerinin üzerinde [https://apache.org](https://apache.org)proje siteleri vardır, örneğin: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Betiğin ne yapar

Bu betik aşağıdaki eylemleri gerçekleştirir:

* Giraph 'yi yükleme`/usr/hdp/current/giraph`

* `giraph-examples.jar` Dosyanızı kümeniz için varsayılan depolama alanına (için) kopyalar:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Betik eylemlerini kullanarak Giraph 'yi yükler

Bir HDInsight kümesine Giraph yüklemek için örnek betik şu konumda bulunabilir:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Bu bölüm, Azure portal kullanarak küme oluştururken örnek betiğin nasıl kullanılacağına ilişkin yönergeler sağlar.

> [!NOTE]  
> Bir betik eylemi, aşağıdaki yöntemlerden herhangi biri kullanılarak uygulanabilir:
> * Azure PowerShell
> * Azure CLI
> * HDInsight .NET SDK 'Sı
> * Azure Resource Manager şablonları
> 
> Zaten çalışan kümelere betik eylemleri de uygulayabilirsiniz. Daha fazla bilgi için bkz. [HDInsight kümelerini betik eylemleriyle özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

1. [Linux tabanlı HDInsight kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md)' daki adımları kullanarak bir küme oluşturmaya başlayın, ancak oluşturma işleminin tamamlamayın.

2. **Isteğe bağlı yapılandırma** bölümünde **betik eylemleri**' ni seçin ve aşağıdaki bilgileri sağlayın:

   * **AD**: Betik eylemi için kolay bir ad girin.

   * **BETİK URI 'Sİ**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **BAŞ**: Bu girdiyi işaretleyin.

   * **ÇALIŞAN**: Bu girdiyi işaretlenmemiş olarak bırakın.

   * **ZOOKEEPER**: Bu girdiyi işaretlenmemiş olarak bırakın.

   * **PARAMETRELER**: Bu alanı boş bırakın.

3. **Betik eylemlerinin**en altında, yapılandırmayı kaydetmek için **Seç** düğmesini kullanın. Son olarak, isteğe bağlı yapılandırma bilgilerini kaydetmek için **Isteğe bağlı yapılandırma** bölümünün altındaki **Seç** düğmesini kullanın.

4. [Linux tabanlı HDInsight kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md)bölümünde açıklandığı gibi Kümeyi oluşturmaya devam edin.

## <a name="usegiraph"></a>HDInsight 'ta Giraph 'yi kullanmak Nasıl yaparım? mı?

Küme oluşturulduktan sonra, Giraph ile birlikte gelen Simplekısaltestpathshesaplama örneğini çalıştırmak için aşağıdaki adımları kullanın. Bu örnek, bir grafikteki nesneler arasındaki en kısa yolu bulmak için temel bir [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) uygulamasını kullanır.

1. SSH kullanarak HDInsight kümesine bağlanma:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

2. **Tiny_graph. txt**adlı bir dosya oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    nano tiny_graph.txt
    ```

    Bu dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Bu veriler, biçimlendirme `[source_id, source_value,[[dest_id], [edge_value],...]]`kullanılarak, yönlendirilmiş bir grafikteki nesneler arasındaki ilişkiyi açıklar. Her satır, bir `source_id` nesne ile bir veya daha fazla `dest_id` nesne arasındaki ilişkiyi temsil eder. , `edge_value` `source_id` Ve arasındaki`dest\_id`bağlantının gücü veya uzaklığı olarak düşünülebilir.

    Çizilmiş ve nesneler arasındaki mesafe olarak değer (veya ağırlık) kullanıldığında, veriler aşağıdaki diyagram gibi görünebilir:

    ![tiny_graph. txt, aralarında değişen mesafe çizgileri olan daireler olarak çizilir](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Dosyayı kaydetmek için **CTRL + X**, sonra **Y**ve son olarak, dosya adını kabul etmek için **ENTER** tuşunu kullanın.

4. HDInsight kümeniz için verileri birincil depolamaya depolamak için aşağıdakileri kullanın:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Şu komutu kullanarak Simplekısaltestpathshesaplama örneğini çalıştırın:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Bu komutla kullanılan parametreler aşağıdaki tabloda açıklanmıştır:

   | Parametre | Ne yapar? |
   | --- | --- |
   | `jar` |Örnekleri içeren jar dosyası. |
   | `org.apache.giraph.GiraphRunner` |Örnekleri başlatmak için kullanılan sınıf. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Kullanılan örnek. Bu örnekte, 1 KIMLIĞI ve grafikteki diğer tüm kimlikler arasındaki en kısa yolu hesaplar. |
   | `-ca mapred.job.tracker` |Küme için baş düğümüne. |
   | `-vif` |Giriş verileri için kullanılacak giriş biçimi. |
   | `-vip` |Giriş veri dosyası. |
   | `-vof` |Çıkış biçimi. Bu örnekte, ID ve değer düz metin olarak. |
   | `-op` |Çıkış konumu. |
   | `-w 2` |Kullanılacak çalışanların sayısı. Bu örnekte, 2. |

    Bunlar hakkında daha fazla bilgi ve Giraph örnekleri ile kullanılan diğer parametreler için bkz. [Giraph hızlı](https://giraph.apache.org/quick_start.html)başlangıcı.

6. İş tamamlandıktan sonra sonuçlar **/example/Out/kısaltestpaths** dizininde depolanır. Çıkış dosyası adları, **kısım-b** ile başlar ve birinci, ikinci, vb. dosyayı belirten bir sayıyla biter. Çıktıyı görüntülemek için aşağıdaki komutu kullanın:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Çıktı aşağıdaki metne benzer şekilde görünür:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    Simplekısaltestpathhesaplama örneği, nesne KIMLIĞI 1 ile başlamak ve diğer nesnelerin en kısa yolunu bulmak için sabit kodlanmış bir örnektir. Çıktı, `destination_id` ve `distance`biçimindedir. , `distance` Nesne kimliği 1 ile hedef kimliği arasındaki kenarlarının değeri (veya ağırlığı) olur.

    Bu verileri görselleştirerek, KIMLIK 1 ve diğer tüm nesneler arasındaki en kısa yolu taşıyarak sonuçları doğrulayabilirsiniz. KIMLIK 1 ve KIMLIK 4 arasındaki en kısa yol 5 ' tir. Bu değer <span style="color:orange">1 ile 3</span>arasındaki toplam uzaklığın ve <span style="color:red">kimlik 3 ve 4</span>' ü arasındadır.

    ![Nesnelerin arasına çizilmiş en kısa yolların bulunduğu daireler](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerinde ton 'U yükleyip kullanın](hdinsight-hadoop-hue-linux.md).
