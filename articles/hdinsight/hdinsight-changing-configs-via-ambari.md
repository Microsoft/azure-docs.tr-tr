---
title: Küme yapılandırmasını iyileştirmek için Apache ambarı-Azure HDInsight
description: Azure HDInsight kümelerini yapılandırmak ve iyileştirmek için Apache ambarı Web Kullanıcı arabirimini kullanın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 15a2c75a7619a815655be0fd9fd3044d86acd057
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150108"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>HDInsight küme yapılandırmasını iyileştirmek için Apache ambarı 'nı kullanma

HDInsight, büyük ölçekli veri işleme uygulamaları için [Apache Hadoop](https://hadoop.apache.org/) kümeleri sağlar. Bu karmaşık çok düğümlü kümelerin yönetilmesi, izlenmesi ve iyileştirgetirilmesi zor olabilir. [Apache ambarı](https://ambari.apache.org/) , HDInsight Linux kümelerini yönetmek ve izlemek için bir Web arabirimidir.  Windows kümeleri için, [REST API ambarı](hdinsight-hadoop-manage-ambari-rest-api.md)'nı kullanın.

Ambarı Web Kullanıcı arabirimini kullanmaya giriş için bkz [. Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md)

`https://CLUSTERNAME.azurehdidnsight.net` konumundaki ambarı, küme kimlik bilgilerinizle oturum açın. İlk ekranda Genel Bakış Panosu görüntülenir.

![Apache ambarı Kullanıcı panosu görüntülendi](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Ambarı Web Kullanıcı arabirimi, Konakları, Hizmetleri, uyarıları, konfigürasyonları ve görünümleri yönetmek için kullanılabilir. Ambarı, HDInsight kümesi oluşturmak, Hizmetleri yükseltmek, yığınları ve sürümleri yönetmek, Konakları kullanımdan kaldırmak veya yeniden komisyon sağlamak ya da kümeye hizmet eklemek için kullanılamaz.

## <a name="manage-your-clusters-configuration"></a>Kümenizin yapılandırmasını yönetme

Yapılandırma ayarları belirli bir hizmeti ayarlamanıza yardımcı olur. Bir hizmetin yapılandırma ayarlarını değiştirmek için **Hizmetler** kenar çubuğundan (solda) hizmeti seçin ve ardından hizmet ayrıntısı sayfasındaki **yapılandırmalar** sekmesine gidin.

![Apache ambarı Hizmetleri kenar çubuğu](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Süs Code Java yığın boyutunu değiştir

Süs Code Java yığın boyutu, küme üzerinde yükleme, dosya sayıları ve blok sayısı gibi birçok etkene bağlıdır. 1 GB varsayılan boyutu çoğu küme ile iyi çalışmaktadır, ancak bazı iş yükleri daha fazla veya daha az bellek gerektirebilir.

Süs Yot Java yığın boyutunu değiştirmek için:

1. Hizmetler **kenar çubuğundan bir i seçin ve** **configs** sekmesine gidin.

    ![Apache ambarı, yapılandırma](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. **Süs Code Java yığın boyutu**ayarını bulun. Belirli bir ayarı yazmak ve bulmak için **filtre** metin kutusunu da kullanabilirsiniz. Ayar adının yanındaki **kalem** simgesini seçin.

    ![Apache ambarı süs Code Java yığın boyutu](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Metin kutusuna yeni değeri yazın ve ardından değişikliği kaydetmek için **ENTER** tuşuna basın.

    ![Ambarı düzenleme süs Code Java yığın size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Süs Code Java yığın boyutu 2 GB 'tan 1 GB olarak değişir.

    ![Düzenlenmiş süs Code Java yığın size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Yapılandırma ekranının üstündeki yeşil **Kaydet** düğmesine tıklayarak yaptığınız değişiklikleri kaydedin.

    ![Ambarı ambarı kayıt yapılandırması](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Apache Hive iyileştirmesi

Aşağıdaki bölümlerde, genel Apache Hive performansını iyileştirmeye yönelik yapılandırma seçenekleri açıklanır.

1. Hive yapılandırma parametrelerini değiştirmek için hizmetler kenar çubuğundan **Hive** ' ı seçin.
1. **Configs** sekmesine gidin.

### <a name="set-the-hive-execution-engine"></a>Hive yürütme altyapısını ayarlama

Hive iki yürütme altyapısı sağlar: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) ve [Apache tez](https://tez.apache.org/). Tez, MapReduce 'den daha hızlıdır. HDInsight Linux kümelerinde varsayılan yürütme altyapısı olarak tez vardır. Yürütme altyapısını değiştirmek için:

1. Hive **yapılandırması** sekmesinde, filtre kutusuna **yürütme altyapısı** yazın.

    ![Apache ambarı arama yürütme altyapısı](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. **Optimizasyon** özelliğinin varsayılan değeri **tez**' dir.

    ![İyileştirme-Apache Tez altyapısı](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Mapto ayarla

Hadoop, tek bir dosyayı birden çok dosyaya bölme (*eşleme*) ve elde edilen dosyaları paralel olarak işleme girişiminde vardır. Mapbir sayı, bölme sayısına bağlıdır. Aşağıdaki iki yapılandırma parametresi, tez yürütme altyapısının bölme sayısını ister:

* `tez.grouping.min-size`: varsayılan 16 MB (16.777.216 bayt) değeri ile gruplandırılmış bölme boyutu için alt sınır.
* `tez.grouping.max-size`: varsayılan değer 1 GB (1.073.741.824 bayt) ile gruplandırılmış bölme boyutunda üst sınır.

Thumb 'in performans kuralı olarak, gecikme süresini artırmak için bu parametrelerin her ikisini de azaltın, daha fazla verimlilik elde edin.

Örneğin, 128 MB 'lık bir veri boyutu için dört Eşleyici görevi ayarlamak için her iki parametreyi de her bir 32 MB (33.554.432 bayt) olarak ayarlamanız gerekir.

1. Sınır parametrelerini değiştirmek için tez hizmetinin **configs** sekmesine gidin. **Genel** paneli genişletin ve `tez.grouping.max-size` ve `tez.grouping.min-size` parametrelerini bulun.

1. Her iki parametreyi de **33.554.432** bayta ayarlayın (32 MB).

    ![Apache ambarı tez gruplama boyutları](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Bu değişiklikler sunucu genelindeki tüm tez işlerini etkiler. En iyi sonucu almak için uygun parametre değerlerini seçin.

### <a name="tune-reducers"></a>Azaltıcının ayarla

[Apache ORC](https://orc.apache.org/) ve [Snappy](https://google.github.io/snappy/) , her ikisi de yüksek performans sunar. Ancak Hive varsayılan olarak çok az sayıda azaltıcının içerebilir ve performans sorunlarına neden olabilir.

Örneğin, 50 GB bir giriş veri boyutunuz olduğunu varsayalım. Snappy sıkıştırması ile ORC biçimindeki veriler 1 GB 'dir. Hive, gereken azaltıcının sayısını şu şekilde tahmin eder: (mapbay/`hive.exec.reducers.bytes.per.reducer`için bayt girişi sayısı).

Varsayılan ayarlarla Bu örnek 4 azaltıcının ' dir.

`hive.exec.reducers.bytes.per.reducer` parametresi, Reducer başına işlenen bayt sayısını belirtir. Varsayılan değer 64 MB 'tır. Bu değeri ayarlamak paralellik artırır ve performansı artırabilir. Büyük olasılıkla performansı olumsuz etkileyecek çok fazla azaltıcının de üretebilirsiniz. Bu parametre, belirli veri gereksinimlerinize, sıkıştırma ayarlarınıza ve diğer çevresel faktörlere göre belirlenir.

1. Parametreyi değiştirmek için Hive **yapılandırması** ' na gidin ve Ayarlar sayfasında **Reducer başına veri** parametresini bulun.

    ![Reducer başına Apache ambarı verileri](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Değeri 128 MB (134.217.728 bayt) olarak değiştirmek için **Düzenle** ' yi seçin ve sonra kaydetmek için **ENTER** tuşuna basın.

    ![Reducer başına veri ambarı verileri düzenlendi](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Reducer başına 128 MB veri ile 1.024 MB 'lık bir giriş boyutu verildiğinde, 8 azaltıcının (1024/128) vardır.

1. **Reducer parametresi başına veriler** için yanlış bir değer, sorgu performansını olumsuz yönde etkileyen çok sayıda azaltıcının oluşmasına neden olabilir. En fazla azaltıcının sayısını sınırlandırmak için `hive.exec.reducers.max` uygun bir değere ayarlayın. Varsayılan değer 1009 ' dir.

### <a name="enable-parallel-execution"></a>Paralel yürütmeyi etkinleştir

Hive sorgusu bir veya daha fazla aşamada yürütülür. Bağımsız aşamalar paralel olarak çalıştırılabilirler, bu, sorgu performansını arttırır.

1. Paralel sorgu yürütmeyi etkinleştirmek için Hive **yapılandırma** sekmesine gidin ve `hive.exec.parallel` özelliğini arayın. Varsayılan değer false'tur. Değeri true olarak değiştirin ve ardından değeri kaydetmek için **ENTER** tuşuna basın.

1. Paralel olarak çalışacak işlerin sayısını sınırlandırmak için `hive.exec.parallel.thread.number` özelliğini değiştirin. Varsayılan değer 8 ' dir.

    ![Apache Hive exec paralel ekran](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Vektörleştirmeyi etkinleştir

Hive, veri satırını satıra göre işler. Vektörleştirme, Hive 'yi aynı anda bir satır yerine 1.024 satırlık bloklar halinde işlemek üzere yönlendirir. Vektörleştirme yalnızca ORC dosya biçimi için geçerlidir.

1. Vektörleştirilmiş bir sorgu yürütmesini etkinleştirmek için Hive **yapılandırması** sekmesine gidin ve `hive.vectorized.execution.enabled` parametresini arayın. Hive 0.13.0 veya üzeri için varsayılan değer true 'dur.

1. Sorgunun azaltılması için vektörleştirilmiş yürütmeyi etkinleştirmek üzere `hive.vectorized.execution.reduce.enabled` parametresini true olarak ayarlayın. Varsayılan değer false'tur.

    ![Apache Hive vektörleştirilmiş yürütme](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Maliyet tabanlı iyileştirmeyi (CBO) etkinleştir

Varsayılan olarak, Hive en iyi bir sorgu yürütme planını bulmak için bir kurallar kümesi izler. Maliyet tabanlı iyileştirme (CBO), bir sorguyu yürütmek için birden çok planı değerlendirir ve her plana bir maliyet atar ve ardından bir sorguyu yürütmek için en ucuz planı belirler.

CBO 'i etkinleştirmek için, **Hive** > **configs** > **Ayarlar** ' a gidin ve **maliyet tabanlı İyileştiriciyi etkinleştir**' i bulun ve geçiş düğmesini **Açık**olarak değiştirin.

![HDInsight maliyet tabanlı iyileştirici](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Aşağıdaki ek yapılandırma parametreleri, CBO etkin olduğunda Hive sorgu performansını artırır:

* `hive.compute.query.using.stats`

    True olarak ayarlandığında, Hive `count(*)`gibi basit sorguları yanıtlamak için meta veri deposu içinde depolanan istatistikleri kullanır.

    ![İstatistikleri kullanarak işlem sorgusunu Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    CBO etkinken sütun istatistikleri oluşturulur. Hive sorguları iyileştirmek için, meta veri deposu içinde depolanan sütun istatistiklerini kullanır. Sütun sayısı yüksek olduğunda her sütun için sütun istatistiklerinin getirilmesi daha uzun sürer. False olarak ayarlandığında, bu ayar sütun istatistiklerini meta veri deposu 'ndan getirmeyi devre dışı bırakır.

    ![Apache Hive stats sütun istatistiklerini ayarla](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Satır sayısı, veri boyutu ve dosya boyutu gibi temel bölüm istatistikleri, meta veri deposu 'nda depolanır. True olarak ayarlandığında, Bölüm istatistikleri meta veri deposu 'ndan getirilir. Yanlış olduğunda dosya boyutu dosya sisteminden getirilir ve satır sayısı satır şemasından alınır.

    ![Hive istatistikleri bölüm istatistiklerini ayarlama](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Ara sıkıştırmayı etkinleştir

Eşleme görevleri Reducer görevleri tarafından kullanılan ara dosyalar oluşturur. Ara sıkıştırma, ara dosya boyutunu küçültür.

Hadoop işleri genellikle g/ç bottlenecked. Verilerin sıkıştırılması, g/ç ve genel ağ aktarımını hızlandırabilir.

Kullanılabilir sıkıştırma türleri şunlardır:

| Biçimlendir | Araç | Algoritmalar | Dosya Uzantısı | Bölünebilir? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | Söndür | . gz | Hayır |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Yes |
| LZO | Lzop | LZO | . LZO | Dizine alınmışsa Evet |
| Snappy | Yok | Snappy | Snappy | Hayır |

Genel bir kural olarak, sıkıştırma yöntemi bölünmüş tablo önemli olduğundan, bazı durumlarda çok az maplıas oluşturulur. Giriş verileri metin ise, en iyi seçenek `bzip2`. ORC biçimi için, Snappy en hızlı sıkıştırma seçeneğidir.

1. Ara sıkıştırmayı etkinleştirmek için Hive **yapılandırması** ' na gidin ve ardından `hive.exec.compress.intermediate` parametresini true olarak ayarlayın. Varsayılan değer false'tur.

    ![Hive exec sıkıştırma ara](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Ara dosyaları sıkıştırmak için, codec, yüksek bir sıkıştırma çıkışına sahip olmasa bile daha düşük CPU maliyetiyle bir sıkıştırma codec bileşeni seçin.

1. Ara sıkıştırma codec bileşenini ayarlamak için, özel özellik `mapred.map.output.compression.codec` `hive-site.xml` veya `mapred-site.xml` dosyasına ekleyin.

1. Özel bir ayar eklemek için:

    a. **Gelişmiş** > **özel Hive sitesi** > **Hive** > **configs** 'a gidin.

    b. Özel Hive sitesi bölmesinin alt kısmındaki **Özellik Ekle...** öğesini seçin.

    c. Özellik Ekle penceresinde, anahtar olarak `mapred.map.output.compression.codec` girin ve değer olarak `org.apache.hadoop.io.compress.SnappyCodec`.

    d. **Add (Ekle)** seçeneğini belirleyin.

    ![Özel özellik ekleme Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Bu, Snappy sıkıştırması kullanılarak ara dosyayı sıkıştırır. Özellik eklendikten sonra, özel Hive sitesi bölmesinde görünür.

    > [!NOTE]  
    > Bu yordam `$HADOOP_HOME/conf/hive-site.xml` dosyasını değiştirir.

### <a name="compress-final-output"></a>Nihai çıktıyı sıkıştır

Son Hive çıktısı da sıkıştırılabilir.

1. Son Hive çıkışını sıkıştırmak için Hive **configs** sekmesine gidin ve ardından `hive.exec.compress.output` parametresini true olarak ayarlayın. Varsayılan değer false'tur.

1. Çıkış sıkıştırma codec bileşenini seçmek için, önceki bölümün adım 3 ' te açıklandığı şekilde özel Hive-site bölmesine `mapred.output.compression.codec` özel özelliğini ekleyin.

    ![Özel özellik Add2 Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Kurgusal yürütmeyi etkinleştir

Öngörülebilir yürütme, yavaş çalışan görev izleyicisinin algılanması ve bunların listesini belirlemek için belirli sayıda yinelenen görevi başlatır. Bu işlem, ayrı görev sonuçlarını en iyi duruma getirerek genel iş yürütmesini geliştirir.

Büyük miktarlarda girişi olan uzun süreli MapReduce görevleri için yansımalı yürütme açık olmamalıdır.

* Kurgusal yürütmeyi etkinleştirmek için Hive **yapılandırması** ' na gidin ve ardından `hive.mapred.reduce.tasks.speculative.execution` parametresini true olarak ayarlayın. Varsayılan değer false'tur.

    ![Hive mapred, görevleri yansımalı yürütmeyi azaltır](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dinamik bölümleri ayarla

Hive, bir tabloya kayıt eklerken, her bölümün ve her bölümün önceden tanımlanması gerekmeden dinamik bölümler oluşturulmasına olanak sağlar. Bu güçlü bir özelliktir, ancak her bölüm için çok sayıda bölüm ve çok sayıda dosya oluşturulmasına neden olabilir.

1. Hive 'nin dinamik bölümleri yapması için `hive.exec.dynamic.partition` parametresi değeri true (varsayılan) olmalıdır.

1. Dinamik bölüm modunu *katı*olarak değiştirin. Katı modda, en az bir bölümün statik olması gerekir. Bu, WHERE yan tümcesinde bölüm filtresi olmadan sorguları önler, diğer bir deyişle, *katı* tüm bölümleri tarayan sorguları önler. Hive **configs** sekmesine gidin ve `hive.exec.dynamic.partition.mode` **katı**olarak ayarlayın. Varsayılan değer **katı olmayan**bir değerdir.

1. Oluşturulacak dinamik bölüm sayısını sınırlandırmak için `hive.exec.max.dynamic.partitions` parametresini değiştirin. Varsayılan değer 5000 ' dir.

1. Düğüm başına toplam dinamik bölüm sayısını sınırlandırmak için `hive.exec.max.dynamic.partitions.pernode`değiştirin. Varsayılan değer 2000 ' dir.

### <a name="enable-local-mode"></a>Yerel modu etkinleştir

Yerel mod, Hive 'ın tek bir makinedeki veya bazen tek bir işlemde bir işin tüm görevlerini gerçekleştirmesini sağlar. Bu, giriş verileri küçükse ve sorgular için görev başlatma yükü genel sorgu yürütmesinin önemli bir yüzdesini kullanırsa sorgu performansını geliştirir.

Yerel modu etkinleştirmek için, [Ara sıkıştırmayı etkinleştir](#enable-intermediate-compression) bölümünün 3. adımında açıklandığı gibi `hive.exec.mode.local.auto` parametresini özel Hive-site paneline ekleyin.

![Apache Hive exec modu yerel otomatik](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Tek MapReduce çok grubunu ayarla

Bu özellik true olarak ayarlandığında, ortak gruplandırma ölçütü olan bir çok gruplama tek bir MapReduce işi oluşturur.  

Bu davranışı etkinleştirmek için, `hive.multigroupby.singlereducer` parametresini, [Ara sıkıştırmayı etkinleştir](#enable-intermediate-compression) bölümünün 3. adımında açıklandığı gibi özel Hive-site bölmesine ekleyin.

![Hive set BY Single MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Ek Hive iyileştirmeleri

Aşağıdaki bölümlerde, ayarlayabileceğiniz, Hive ile ilgili ek iyileştirmeler açıklanır.

#### <a name="join-optimizations"></a>En iyi duruma getirme

Hive içindeki varsayılan JOIN türü bir *karışık birleşimdir*. Hive 'de özel mapvın girişi okur ve bir ara dosyaya bir JOIN anahtar/değer çifti yayar. Hadoop, bu çiftleri bir karışık aşamada sıralar ve birleştirir. Bu karışık aşama pahalıdır. Verilerinize göre doğru katılmayı seçmek performansı önemli ölçüde iyileştirebilir.

| JOIN türü | Oluşturulurken | Oluşturulacağı | Hive ayarları | Yorumlar |
| -- | -- | -- | -- | -- |
| Karışık ekleme | <ul><li>Varsayılan seçenek</li><li>Her zaman çalışma</li></ul> | <ul><li>Tablolardan birinin bir bölümünden okur</li><li>Demetler ve JOIN anahtarına göre sıralar</li><li>Her küçültme için bir demet gönderir</li><li>Düşürme, azaltma tarafında yapılır</li></ul> | Önemli Hive ayarı gerekmiyor | Her seferinde çalışma |
| Eşleme birleşimi | <ul><li>Bir tablo, belleğe sığmayacak</li></ul> | <ul><li>Küçük tabloyu bellek karması tablosuna okur</li><li>Büyük dosyanın bir parçası aracılığıyla akışlar</li><li>Karma tablodaki her kaydı birleştirir</li><li>Birleşimler eşleştiriciyle tek başına</li></ul> | `hive.auto.confvert.join=true` | Çok hızlı, ancak sınırlı |
| Birleştirme demetini Sırala | Her iki tablo ise: <ul><li>Aynı sıralama</li><li>Bulaştırılmış aynı</li><li>Sıralanan/bucketed sütununa katılma</li></ul> | Her işlem: <ul><li>Her tablodan bir demet okur</li><li>Satırı en düşük değerle işler</li></ul> | `hive.auto.convert.sortmerge.join=true` | Çok verimli |

#### <a name="execution-engine-optimizations"></a>Yürütme altyapısı iyileştirmeleri

Hive yürütme altyapısını iyileştirmeye yönelik ek öneriler:

| Ayar | Önerilen | HDInsight varsayılan |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = daha güvenli, daha yavaş; yanlış = daha hızlı | yanlış |
| `tez.am.resource.memory.mb` | en çok 4 GB üst sınır | Otomatik olarak ayarlanmış |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig iyileştirmesi

[Apache Pig](https://pig.apache.org/) özellikleri, Pig sorgularını ayarlamak Için, ambarı Web kullanıcı arabiriminden değiştirilebilir. Pig özelliklerinin doğrudan ambarı 'nda değiştirilmesi `/etc/pig/2.4.2.0-258.0/pig.properties` dosyadaki Pig özelliklerini değiştirir.

1. Pig özelliklerini değiştirmek için Pig **configs** sekmesine gidin ve ardından **Gelişmiş Pig-Özellikler** bölmesini genişletin.

1. Değiştirmek istediğiniz özelliğin değerini bulun, açıklama kaldırın ve değiştirin.

1. Yeni değeri kaydetmek için pencerenin sağ üst tarafındaki **Kaydet** ' i seçin. Bazı özellikler, bir hizmetin yeniden başlatılmasını gerektirebilir.

    ![Gelişmiş Apache Pig özellikleri](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Tüm oturum düzeyi ayarları `pig.properties` dosyasındaki özellik değerlerini geçersiz kılar.

### <a name="tune-execution-engine"></a>Yürütme altyapısını ayarla

Pig betiklerini yürütmek için iki yürütme altyapısı mevcuttur: MapReduce ve tez. Tez, iyileştirilmiş bir altyapıdır ve MapReduce 'den çok daha hızlıdır.

1. Yürütme altyapısını değiştirmek için **Gelişmiş Pig-Özellikler** bölmesinde `exectype`özelliğini bulun.

1. Varsayılan değer **MapReduce**' dir. **Tez**olarak değiştirin.

### <a name="enable-local-mode"></a>Yerel modu etkinleştir

Hive ile benzer şekilde, yerel mod, işleri görece daha az miktarda veri ile hızlandırmak için kullanılır.

1. Yerel modu etkinleştirmek için `pig.auto.local.enabled` **true**olarak ayarlayın. Varsayılan değer false'tur.

1. Giriş veri boyutu `pig.auto.local.input.maxbytes` özellik değerinden küçük olan işlerin küçük işler olduğu kabul edilir. Varsayılan değer 1 GB 'tır.

### <a name="copy-user-jar-cache"></a>Kullanıcı jar önbelleğini Kopyala

Pig, UDF 'ler için gereken JAR dosyalarını görev düğümleri için kullanılabilir hale getirmek üzere dağıtılmış bir önbelleğe kopyalar. Bu jliler sıklıkla değişmez. Etkinleştirilirse, `pig.user.cache.enabled` ayarı jar dosyaları dışındaki 'ın aynı kullanıcı tarafından çalıştırılan işler için yeniden kullanmak üzere bir önbelleğe yerleştirilmesine olanak tanır. Bu, İş performansının küçük bir artmasına neden olur.

1. Etkinleştirmek için `pig.user.cache.enabled` true olarak ayarlayın. Varsayılan değer false 'dur.

1. Önbelleğe alınan jars 'ın temel yolunu ayarlamak için `pig.user.cache.location` temel yola ayarlayın. Varsayılan değer: `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Bellek ayarlarıyla performansı iyileştirin

Aşağıdaki bellek ayarları Pig betiği performansını iyileştirmenize yardımcı olabilir.

* `pig.cachedbag.memusage`: bir paket için ayrılan bellek miktarı. Bir paket, tanımlama grupları koleksiyonudur. Kayıt düzeni sıralı bir alan kümesidir ve bir alan veri parçasıdır. Bir paket içindeki veriler, ayrılan belleğin ötesinde, diske taşılır. Varsayılan değer, kullanılabilir belleğin yüzde 20 ' sini temsil eden 0,2 ' dir. Bu bellek, bir uygulamadaki tüm baorda paylaşılır.

* `pig.spill.size.threshold`: bu taşma boyutundan (bayt olarak) daha büyük olan paketler diske taşmıştır. Varsayılan değer 5 MB 'tır.

### <a name="compress-temporary-files"></a>Geçici dosyaları sıkıştır

Pig, iş yürütülürken geçici dosyalar oluşturur. Dosyaları okurken veya diske yazarken geçici dosyaları sıkıştırmak performans artışına neden olur. Geçici dosyaları sıkıştırmak için aşağıdaki ayarlar kullanılabilir.

* `pig.tmpfilecompression`: doğru olduğunda geçici dosya sıkıştırmasını etkinleştirilir. Varsayılan değer false'tur.

* `pig.tmpfilecompression.codec`: geçici dosyaları sıkıştırmak için kullanılacak sıkıştırma codec bileşeni. Önerilen sıkıştırma codec bileşenleri, daha düşük CPU kullanımı için [LZO](https://www.oberhumer.com/opensource/lzo/) ve Snappy ' dir.

### <a name="enable-split-combining"></a>Bölünmüş birleştirmeyi etkinleştir

Etkinleştirildiğinde, küçük dosyalar daha az eşleme görevi için birleştirilir. Bu, birçok küçük dosya içeren işlerin verimliliğini artırır. Etkinleştirmek için `pig.noSplitCombination` true olarak ayarlayın. Varsayılan değer false'tur.

### <a name="tune-mappers"></a>Mapto ayarla

Eşleme sayısı `pig.maxCombinedSplitSize`özelliği değiştirilerek denetlenir. Bu, tek bir eşleme göreviyle işlenecek verilerin boyutunu belirtir. Varsayılan değer FileSystem 'ın varsayılan blok boyutudur. Bu değerin artırılması, Eşleyici görevlerinin sayısının azalmasına neden olur.

### <a name="tune-reducers"></a>Azaltıcının ayarla

Azaltıcının sayısı `pig.exec.reducers.bytes.per.reducer`parametresi temel alınarak hesaplanır. Parametresi, varsayılan 1 GB olarak Reducer başına işlenen bayt sayısını belirtir. En fazla azaltıcının sayısını sınırlandırmak için, `pig.exec.reducers.max` özelliğini varsayılan 999 olarak ayarlayın.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Ambarı Web Kullanıcı arabirimi ile Apache HBase iyileştirmesi

[Apache HBase](https://hbase.apache.org/) yapılandırması **HBase configs** sekmesinden değiştirilir. Aşağıdaki bölümlerde, HBase performansını etkileyen bazı önemli yapılandırma ayarları açıklanır.

### <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE ayarla

HBase yığın boyutu, *bölgeye* ve *ana* sunuculara göre megabayt cinsinden kullanılacak maksimum yığın miktarını belirtir. Varsayılan değer 1.000 MB 'tır. Bu, küme iş yükünün ayarlanmış olması gerekir.

1. Değiştirmek için, HBase **configs** sekmesindeki **Gelişmiş HBase-env** bölmesine gidin ve `HBASE_HEAPSIZE` ayarını bulun.

1. Varsayılan değeri 5.000 MB olarak değiştirin.

    ![Apache ambarı HBase belleği HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Okuma ağır iş yüklerini iyileştirme

Okuma ağır iş yüklerinin performansını artırmak için aşağıdaki konfigürasyonlar önemlidir.

#### <a name="block-cache-size"></a>Blok önbelleği boyutu

Blok önbelleği, okuma önbelleğidir. Boyutu `hfile.block.cache.size` parametresi tarafından denetlenir. Varsayılan değer, toplam bölge sunucu belleğinin yüzde 40 ' i olan 0,4 ' dir. Blok önbelleği boyutunun ne kadar büyük olması, rastgele okumaların daha hızlı olması olur.

1. Bu parametreyi değiştirmek için, HBase **configs** sekmesindeki **Ayarlar** sekmesine gidin ve ardından **okuma arabelleklerine ayrılan regionserver 'ın%** ' i bulun.

    ![Apache HBase bellek bloğu önbellek boyutu](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Değeri değiştirmek için **Düzenle** simgesini seçin.

#### <a name="memstore-size"></a>Memstore boyutu

Tüm düzenlemeler, bellek arabelleğinde saklanır, bu, *Memstore*olarak adlandırılır. Bu, tek bir işlemde diske yazılabilen toplam veri miktarını artırır ve son düzenlemelere sonraki erişimi hızlandırır. Memstore boyutu aşağıdaki iki parametre tarafından tanımlanır:

* `hbase.regionserver.global.memstore.UpperLimit`: en yüksek bölge sunucusu için, Memstore Birleşik tarafından kullanılabilecek maksimum yüzdeyi tanımlar.

* `hbase.regionserver.global.memstore.LowerLimit`: bölge sunucusunun Memstore birleşiminin kullanabileceği minimum yüzdesini tanımlar.

Rastgele okumaları iyileştirmek için, Memstore üst ve alt sınırlarını azaltabilirsiniz.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Diskten tarama sırasında getirilen satır sayısı

`hbase.client.scanner.caching` ayarı, bir tarayıcıda `next` yöntemi çağrıldığında diskten okunan satır sayısını tanımlar.  Varsayılan değer 100’dür. Sayı arttıkça, istemciden bölge sunucusuna yapılan uzak çağrılar daha az tarama elde edilir. Bununla birlikte, bu, istemcideki bellek basıncını de artırır.

![Getirilen Apache HBase satır sayısı](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Bir tarayıcıdaki sonraki yöntemin çağrılması ile ilgili saatin tarayıcı zaman aşımından daha büyük olması için değeri ayarlamayın. Tarayıcı zaman aşımı süresi `hbase.regionserver.lease.period` özelliği tarafından tanımlanır.

### <a name="optimize-write-heavy-workloads"></a>Yazma ağır iş yüklerini iyileştirme

Aşağıdaki konfigürasyonlar, yazma ağır iş yüklerinin performansını artırmak için önemlidir.

#### <a name="maximum-region-file-size"></a>En büyük bölge dosyası boyutu

HBase verileri *hfile*adlı dahili bir dosya biçiminde depolar. Özellik `hbase.hregion.max.filesize` bir bölge için tek bir HFile boyutunu tanımlar.  Bir bölgedeki tüm HFiles değerlerinin bu ayardan büyük olması halinde bölge iki bölgeye ayrılır.

![Apache HBase HRegion maksimum dosya boyutu](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Bölge dosya boyutu ne kadar büyükse, bölme sayısı küçülür. En fazla yazma performansına neden olan bir değeri öğrenmek için dosya boyutunu artırabilirsiniz.

#### <a name="avoid-update-blocking"></a>Güncelleştirme engellemeyi önleyin

* Özellik `hbase.hregion.memstore.flush.size`, Memstore 'nin diske boşaltılmakta olduğu boyutu tanımlar. Varsayılan boyut 128 MB 'tır.

* HBase bölgesi blok çarpanı `hbase.hregion.memstore.block.multiplier`tarafından tanımlanır. Varsayılan değer 4'tür. İzin verilen en fazla 8.

* Memstore (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bayt ise, HBase güncelleştirmeleri kaldırır.

    Varsayılan Temizleme boyutu ve blok çarpanı değerleri ile, Memstore boyutu 128 * 4 = 512 MB olduğunda güncelleştirmeler engellenir. Güncelleştirme engelleme sayısını azaltmak için `hbase.hregion.memstore.block.multiplier`değerini artırın.

![Apache HBase bölgesi blok çarpanı](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Memstore boyutunu tanımla

Memstore boyutu `hbase.regionserver.global.memstore.UpperLimit` ve `hbase.regionserver.global.memstore.LowerLimit` parametreleri tarafından tanımlanır. Bu değerlerin birbirlerine eşit ayarlanması yazma sırasında duraklar azaltır (Ayrıca daha sık temizlemeye devam ediyor) ve daha fazla yazma performansı elde edin.

### <a name="set-memstore-local-allocation-buffer"></a>Memstore yerel ayırma arabelleğini ayarla

Memstore yerel ayırma arabelleği kullanımı, `hbase.hregion.memstore.mslab.enabled`özellik tarafından belirlenir. Etkinleştirildiğinde (true), bu, ağır yazma işlemi sırasında yığın parçalanmasını önler. Varsayılan değer true olur.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini Apache ambarı Web Kullanıcı arabirimi ile yönetme](hdinsight-hadoop-manage-ambari.md)
* [Apache ambarı REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
