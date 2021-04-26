---
title: Azure HDInsight 'ta Apache ambarı ile Apache Hive iyileştirin
description: Apache Hive yapılandırmak ve iyileştirmek için Apache ambarı Web Kullanıcı arabirimini kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 69a4e769677b6f0200f4157305a3a125f82ee76d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864826"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache ambarı ile Apache Hive iyileştirin

Apache ambarı, HDInsight kümelerini yönetmek ve izlemek için bir Web arabirimidir. Ambarı Web Kullanıcı arabirimine giriş için bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md).

Aşağıdaki bölümlerde, genel Apache Hive performansını iyileştirmeye yönelik yapılandırma seçenekleri açıklanır.

1. Hive yapılandırma parametrelerini değiştirmek için hizmetler kenar çubuğundan **Hive** ' ı seçin.
1. **Configs** sekmesine gidin.

## <a name="set-the-hive-execution-engine"></a>Hive yürütme altyapısını ayarlama

Hive iki yürütme altyapısı sağlar: Apache Hadoop MapReduce ve Apache TEZ. Tez, MapReduce 'den daha hızlıdır. HDInsight Linux kümelerinde varsayılan yürütme altyapısı olarak tez vardır. Yürütme altyapısını değiştirmek için:

1. Hive **yapılandırması** sekmesinde, filtre kutusuna **yürütme altyapısı** yazın.

    :::image type="content" source="./media/optimize-hive-ambari/ambari-search-execution.png" alt-text="Apache ambarı arama yürütme altyapısı" border="true":::

1. **Optimizasyon** özelliğinin varsayılan değeri **tez**' dir.

    :::image type="content" source="./media/optimize-hive-ambari/optimization-apache-tez.png" alt-text="İyileştirme-Apache Tez altyapısı" border="true":::

## <a name="tune-mappers"></a>Mapto ayarla

Hadoop, tek bir dosyayı birden çok dosyaya bölme (*eşleme*) ve elde edilen dosyaları paralel olarak işleme girişiminde vardır. Mapbir sayı, bölme sayısına bağlıdır. Aşağıdaki iki yapılandırma parametresi, tez yürütme altyapısının bölme sayısını ister:

* `tez.grouping.min-size`: Varsayılan 16 MB (16.777.216 bayt) değeri ile gruplandırılmış bölme boyutu için alt sınır.
* `tez.grouping.max-size`: Varsayılan değeri 1 GB (1.073.741.824 bayt) olan gruplandırılmış bölme boyutunda üst sınır.

Performans Kılavuzu olarak, gecikme süresini artırmak için bu parametrelerin her ikisini de düşürün, daha fazla verimlilik için artış yapın.

Örneğin, 128 MB 'lık bir veri boyutu için dört Eşleyici görevi ayarlamak için her iki parametreyi de her bir 32 MB (33.554.432 bayt) olarak ayarlamanız gerekir.

1. Sınır parametrelerini değiştirmek için tez hizmetinin **configs** sekmesine gidin. **Genel** panelini genişletin ve `tez.grouping.max-size` ve `tez.grouping.min-size` parametrelerini bulun.

1. Her iki parametreyi de **33.554.432** bayta ayarlayın (32 MB).

    :::image type="content" source="./media/optimize-hive-ambari/apache-tez-grouping-size.png" alt-text="Apache ambarı tez gruplama boyutları" border="true":::

Bu değişiklikler sunucu genelindeki tüm tez işlerini etkiler. En iyi sonucu almak için uygun parametre değerlerini seçin.

## <a name="tune-reducers"></a>Azaltıcının ayarla

Apache ORC ve Snappy, her ikisi de yüksek performans sunar. Ancak Hive varsayılan olarak çok az sayıda azaltıcının içerebilir ve performans sorunlarına neden olabilir.

Örneğin, 50 GB bir giriş veri boyutunuz olduğunu varsayalım. Snappy sıkıştırması ile ORC biçimindeki veriler 1 GB 'dir. Hive gereken azaltıcının sayısını şu şekilde tahmin eder: (mapvıd için bayt girişi sayısı/ `hive.exec.reducers.bytes.per.reducer` ).

Varsayılan ayarlarla Bu örnek dört azaltıcının.

`hive.exec.reducers.bytes.per.reducer`Parametresi, Reducer başına işlenen bayt sayısını belirtir. Varsayılan değer 64 MB 'tır. Bu değeri ayarlamak paralellik artırır ve performansı artırabilir. Büyük olasılıkla performansı olumsuz etkileyecek çok fazla azaltıcının de üretebilirsiniz. Bu parametre, belirli veri gereksinimlerinize, sıkıştırma ayarlarınıza ve diğer çevresel faktörlere göre belirlenir.

1. Parametreyi değiştirmek için Hive **yapılandırması** ' na gidin ve Ayarlar sayfasında **Reducer başına veri** parametresini bulun.

    :::image type="content" source="./media/optimize-hive-ambari/ambari-data-per-reducer.png" alt-text="Reducer başına Apache ambarı verileri" border="true":::

1. Değeri 128 MB (134.217.728 bayt) olarak değiştirmek için **Düzenle** ' yi seçin ve sonra kaydetmek için **ENTER** tuşuna basın.

    :::image type="content" source="./media/optimize-hive-ambari/data-per-reducer-edited.png" alt-text="Reducer başına veri ambarı verileri düzenlendi" border="true":::
  
    Reducer başına 128 MB veri ile 1.024 MB 'lık bir giriş boyutu verildiğinde, sekiz azaltıcının (1024/128) vardır.

1. **Reducer parametresi başına veriler** için yanlış bir değer, sorgu performansını olumsuz yönde etkileyen çok sayıda azaltıcının oluşmasına neden olabilir. Maksimum azaltıcının sayısını sınırlandırmak için `hive.exec.reducers.max` uygun bir değere ayarlayın. Varsayılan değer 1009 ' dir.

## <a name="enable-parallel-execution"></a>Paralel yürütmeyi etkinleştir

Hive sorgusu bir veya daha fazla aşamada yürütülür. Bağımsız aşamalar paralel olarak çalıştırılabilirler, bu, sorgu performansını arttırır.

1. Paralel sorgu yürütmeyi etkinleştirmek için Hive **yapılandırma** sekmesine gidin ve `hive.exec.parallel` özelliğini arayın. Varsayılan değer false'tur. Değeri true olarak değiştirin ve ardından değeri kaydetmek için **ENTER** tuşuna basın.

1. Paralel olarak çalışacak işlerin sayısını sınırlandırmak için `hive.exec.parallel.thread.number` özelliği değiştirin. Varsayılan değer 8 ' dir.

    :::image type="content" source="./media/optimize-hive-ambari/apache-hive-exec-parallel.png" alt-text="Apache Hive exec paralel ekran" border="true":::

## <a name="enable-vectorization"></a>Vektörleştirmeyi etkinleştir

Hive, veri satırını satıra göre işler. Vektörleştirme, Hive 'yi aynı anda bir satır yerine 1.024 satırlık bloklar halinde işlemek üzere yönlendirir. Vektörleştirme yalnızca ORC dosya biçimi için geçerlidir.

1. Vektörleştirilmiş bir sorgu yürütmesini etkinleştirmek için Hive **yapılandırması** ' na gidin ve `hive.vectorized.execution.enabled` parametreyi arayın. Hive 0.13.0 veya üzeri için varsayılan değer true 'dur.

1. Sorgunun azaltılması için vektörleştirilmiş yürütmeyi etkinleştirmek üzere `hive.vectorized.execution.reduce.enabled` parametresini true olarak ayarlayın. Varsayılan değer false'tur.

    :::image type="content" source="./media/optimize-hive-ambari/hive-vectorized-execution.png" alt-text="Apache Hive vektörleştirilmiş yürütme" border="true":::

## <a name="enable-cost-based-optimization-cbo"></a>Maliyet tabanlı iyileştirmeyi (CBO) etkinleştir

Varsayılan olarak, Hive en iyi bir sorgu yürütme planını bulmak için bir kurallar kümesi izler. Maliyet tabanlı iyileştirme (CBO), bir sorguyu yürütmek için birden çok planı değerlendirir. Ve her plana bir maliyet atar ve ardından bir sorgu yürütmek için en ucuz planı belirler.

CBO 'i etkinleştirmek için **Hive**  >  **configs**  >  **ayarları** ' na gidin ve **maliyet tabanlı İyileştiriciyi etkinleştir**' i bulun ve geçiş düğmesini **Açık** olarak değiştirin.

:::image type="content" source="./media/optimize-hive-ambari/hdinsight-cbo-config.png" alt-text="HDInsight maliyet tabanlı iyileştirici" border="true":::

Aşağıdaki ek yapılandırma parametreleri, CBO etkin olduğunda Hive sorgu performansını artırır:

* `hive.compute.query.using.stats`

    True olarak ayarlandığında Hive, gibi basit sorguları yanıtlamak için meta veri deposu içinde depolanan istatistikleri kullanır `count(*)` .

    :::image type="content" source="./media/optimize-hive-ambari/hive-compute-query-using-stats.png" alt-text="İstatistikleri kullanarak işlem sorgusunu Apache Hive" border="true":::

* `hive.stats.fetch.column.stats`

    CBO etkinken sütun istatistikleri oluşturulur. Hive sorguları iyileştirmek için, meta veri deposu içinde depolanan sütun istatistiklerini kullanır. Sütun sayısı yüksek olduğunda her sütun için sütun istatistiklerinin getirilmesi daha uzun sürer. False olarak ayarlandığında, bu ayar sütun istatistiklerini meta veri deposu 'ndan getirmeyi devre dışı bırakır.

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png" alt-text="Apache Hive stats sütun istatistiklerini ayarla" border="true":::

* `hive.stats.fetch.partition.stats`

    Satır sayısı, veri boyutu ve dosya boyutu gibi temel bölüm istatistikleri, meta veri deposu 'nda depolanır. True olarak ayarlanırsa, Bölüm istatistikleri meta veri deposu 'ndan getirilir. Yanlış olduğunda dosya boyutu dosya sisteminden getirilir. Ve satır sayısı satır şemasından getirilir.

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png" alt-text="Hive istatistikleri bölüm istatistiklerini ayarlama" border="true":::

## <a name="enable-intermediate-compression"></a>Ara sıkıştırmayı etkinleştir

Eşleme görevleri Reducer görevleri tarafından kullanılan ara dosyalar oluşturur. Ara sıkıştırma, ara dosya boyutunu küçültür.

Hadoop işleri genellikle g/ç bottlenecked. Verilerin sıkıştırılması, g/ç ve genel ağ aktarımını hızlandırabilir.

Kullanılabilir sıkıştırma türleri şunlardır:

| Biçimlendir | Araç | Algoritma | Dosya Uzantısı | Bölünebilir? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | Söndür | `.gz` | No |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Yes |
| LZO | `Lzop` | LZO | `.lzo` | Dizine alınmışsa Evet |
| Snappy | Yok | Snappy | Snappy | No |

Genel bir kural olarak, sıkıştırma yöntemi bölünmüş tablo önemli olduğundan, bazı durumlarda birkaç mapas oluşturulur. Giriş verileri metin ise `bzip2` en iyi seçenektir. ORC biçimi için, Snappy en hızlı sıkıştırma seçeneğidir.

1. Ara sıkıştırmayı etkinleştirmek için Hive **yapılandırması** ' na gidin ve ardından `hive.exec.compress.intermediate` parametreyi doğru olarak ayarlayın. Varsayılan değer false'tur.

    :::image type="content" source="./media/optimize-hive-ambari/hive-exec-compress-intermediate.png" alt-text="' Hive exec sıkıştırma ara '" border="true":::

    > [!NOTE]  
    > Ara dosyaları sıkıştırmak için, codec, yüksek bir sıkıştırma çıkışına sahip olmasa bile daha düşük CPU maliyetiyle bir sıkıştırma codec bileşeni seçin.

1. Ara sıkıştırma codec bileşenini ayarlamak için, veya dosyasına özel özelliği ekleyin `mapred.map.output.compression.codec` `hive-site.xml` `mapred-site.xml` .

1. Özel bir ayar eklemek için:

    a. **Hive**  >  **configs**  >  **Gelişmiş**  >  **özel Hive-site** bölümüne gidin.

    b. Özel Hive sitesi bölmesinin alt kısmındaki **Özellik Ekle...** öğesini seçin.

    c. Özellik Ekle penceresinde, `mapred.map.output.compression.codec` anahtar olarak ve `org.apache.hadoop.io.compress.SnappyCodec` değerini girin.

    d. **Add (Ekle)** seçeneğini belirleyin.

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property.png" alt-text="' Apache Hive özel Özellik Ekle '" border="true":::

    Bu ayar, Snappy sıkıştırması kullanılarak ara dosyayı sıkıştırır. Özellik eklendikten sonra, özel Hive sitesi bölmesinde görünür.

    > [!NOTE]  
    > Bu yordam dosyayı değiştirir `$HADOOP_HOME/conf/hive-site.xml` .

## <a name="compress-final-output"></a>Nihai çıktıyı sıkıştır

Son Hive çıktısı da sıkıştırılabilir.

1. Son Hive çıkışını sıkıştırmak için Hive **configs** sekmesine gidin ve ardından `hive.exec.compress.output` parametreyi true olarak ayarlayın. Varsayılan değer false'tur.

1. Çıkış sıkıştırma codec bileşenini seçmek için, `mapred.output.compression.codec` önceki bölümün adım 3 ' te açıklandığı gibi özel Hive-site bölmesine özel özelliği ekleyin.

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property2.png" alt-text="Özel özellik Add2 Apache Hive" border="true":::

## <a name="enable-speculative-execution"></a>Kurgusal yürütmeyi etkinleştir

Yansımalı yürütme, yavaş çalışan görev izleyicisini algılamak ve reddetmek için belirli sayıda yinelenen görevi başlatır. Tek tek görev sonuçlarını en iyi duruma getirerek genel iş yürütmesini geliştirirken.

Büyük miktarlarda girişi olan uzun süreli MapReduce görevleri için yansımalı yürütme açık olmamalıdır.

* Yansımalı yürütmeyi etkinleştirmek için Hive **yapılandırması** ' na gidin ve ardından `hive.mapred.reduce.tasks.speculative.execution` parametreyi doğru olarak ayarlayın. Varsayılan değer false'tur.

    :::image type="content" source="./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png" alt-text="' Hive mapred görevleri yansımalı yürütmeyi azaltır '" border="true":::

## <a name="tune-dynamic-partitions"></a>Dinamik bölümleri ayarla

Hive, her bölümü önceden tanımlamaya gerek kalmadan bir tabloya kayıt eklerken dinamik bölümler oluşturulmasına olanak sağlar. Bu yetenek güçlü bir özelliktir. Ancak, çok sayıda bölüm oluşturulmasına neden olabilir. Ve her bölüm için çok sayıda dosya.

1. Hive 'nin dinamik bölümleri yapması için `hive.exec.dynamic.partition` parametre değeri true (varsayılan) olmalıdır.

1. Dinamik bölüm modunu *katı* olarak değiştirin. Katı modda, en az bir bölümün statik olması gerekir. Bu ayar WHERE yan tümcesinde bölüm filtresi olmadan sorguları engeller, diğer bir deyişle, *katı* tüm bölümleri tarayan sorguları önler. Hive **configs** sekmesine gidin ve `hive.exec.dynamic.partition.mode` **katı** olarak ayarlayın. Varsayılan değer **katı olmayan** bir değerdir.

1. Oluşturulacak dinamik bölüm sayısını sınırlandırmak için `hive.exec.max.dynamic.partitions` parametreyi değiştirin. Varsayılan değer 5000 ' dir.

1. Düğüm başına toplam dinamik bölüm sayısını sınırlamak için değiştirin `hive.exec.max.dynamic.partitions.pernode` . Varsayılan değer 2000 ' dir.

## <a name="enable-local-mode"></a>Yerel modu etkinleştir

Yerel mod, Hive 'nin tek bir makinedeki bir işin tüm görevlerini gerçekleştirmesine olanak sağlar. Ya da bazen tek bir işlemde. Bu ayar, giriş verileri küçükse sorgu performansını geliştirir. Ve sorgular için görevler başlatma yükü, genel sorgu yürütmesinin önemli bir yüzdesini tüketir.

Yerel modu etkinleştirmek için, `hive.exec.mode.local.auto` [Ara sıkıştırmayı etkinleştir](#enable-intermediate-compression) bölümünün 3. adımında açıklandığı gibi parametreyi özel Hive-site paneline ekleyin.

:::image type="content" source="./media/optimize-hive-ambari/hive-exec-mode-local-auto.png" alt-text="Apache Hive exec modu yerel otomatik" border="true":::

## <a name="set-single-mapreduce-multigroup-by"></a>Tek MapReduce çok grubunu ayarla

Bu özellik true olarak ayarlandığında, ortak gruplandırma ölçütü olan bir çok gruplama tek bir MapReduce işi oluşturur.  

Bu davranışı etkinleştirmek için `hive.multigroupby.singlereducer` parametreyi, [Ara sıkıştırmayı etkinleştir](#enable-intermediate-compression) bölümünün 3. adımında açıklandığı gibi özel Hive-site bölmesine ekleyin.

:::image type="content" source="./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png" alt-text="Hive set BY Single MapReduce MultiGROUP" border="true":::

## <a name="additional-hive-optimizations"></a>Ek Hive iyileştirmeleri

Aşağıdaki bölümlerde, ayarlayabileceğiniz, Hive ile ilgili ek iyileştirmeler açıklanır.

### <a name="join-optimizations"></a>En iyi duruma getirme

Hive içindeki varsayılan JOIN türü bir *karışık birleşimdir*. Hive 'de özel mapvın girişi okur ve bir ara dosyaya bir JOIN anahtar/değer çifti yayar. Hadoop, bu çiftleri bir karışık aşamada sıralar ve birleştirir. Bu karışık aşama pahalıdır. Verilerinize göre doğru katılmayı seçmek performansı önemli ölçüde iyileştirebilir.

| JOIN türü | Ne zaman | Nasıl? | Hive ayarları | Yorumlar |
| --- | --- | --- | --- | --- |
| Karışık ekleme | <ul><li>Varsayılan seçenek</li><li>Her zaman çalışma</li></ul> | <ul><li>Tablolardan birinin bir bölümünden okur</li><li>Demetler ve JOIN anahtarına göre sıralar</li><li>Her küçültme için bir demet gönderir</li><li>Düşürme, azaltma tarafında yapılır</li></ul> | Önemli Hive ayarı gerekmiyor | Her seferinde çalışma |
| Eşleme birleşimi | <ul><li>Bir tablo, belleğe sığmayacak</li></ul> | <ul><li>Küçük tabloyu bellek karması tablosuna okur</li><li>Büyük dosyanın bir parçası aracılığıyla akışlar</li><li>Karma tablodaki her kaydı birleştirir</li><li>Birleşimler eşleştiriciyle tek başına</li></ul> | `hive.auto.confvert.join=true` | Hızlı, ancak sınırlı |
| Birleştirme demetini Sırala | Her iki tablo ise: <ul><li>Aynı sıralama</li><li>Bulaştırılmış aynı</li><li>Sıralanan/bucketed sütununa katılma</li></ul> | Her işlem: <ul><li>Her tablodan bir demet okur</li><li>Satırı en düşük değerle işler</li></ul> | `hive.auto.convert.sortmerge.join=true` | Verimli |

### <a name="execution-engine-optimizations"></a>Yürütme altyapısı iyileştirmeleri

Hive yürütme altyapısını iyileştirmeye yönelik ek öneriler:

| Ayar | Önerilen | HDInsight varsayılan |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = daha güvenli, daha yavaş; yanlış = daha hızlı | yanlış |
| `tez.am.resource.memory.mb` | en çok 4 GB üst sınır | Otomatik olarak ayarlanmış |
| `tez.session.am.dag.submit.timeout.secs` | 300 + | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000 + | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000 + | 20000 |

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini Apache ambarı Web Kullanıcı arabirimi ile yönetme](hdinsight-hadoop-manage-ambari.md)
* [Apache ambarı REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Azure HDInsight’ta Apache Hive sorgularını iyileştirme](./hdinsight-hadoop-optimize-hive-query.md)
* [Kümeleri iyileştirme](./optimize-hive-ambari.md)
* [Apache HBase’i iyileştirme](./optimize-hbase-ambari.md)
* [Apache Pig’i iyileştirme](./optimize-pig-ambari.md)
