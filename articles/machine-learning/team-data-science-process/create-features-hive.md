---
title: Azure HDInsight Hadoop kümesinde veri için özellikler oluşturma-Team Data Science Işlemi
description: Azure HDInsight Hadoop kümesinde depolanan verilerde Özellikler üreten Hive sorgularının örnekleri.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 30c0a02c2cbc11002f8e0bf0295dab91de5d0365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96020594"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Hive sorguları kullanarak Hadoop kümesindeki veriler için özellikler oluşturma
Bu belgede, Hive sorguları kullanılarak bir Azure HDInsight Hadoop kümesinde depolanan veriler için nasıl özellik oluşturulacağı gösterilmektedir. Bu Hive sorguları, için sunulan komut dosyalarını, yerleşik Hive User-Defined Işlevlerini (UDF) kullanır.

Özellik oluşturmak için gereken işlemler bellek açısından yoğun olabilir. Hive sorgularının performansı bu gibi durumlarda daha kritik hale gelir ve belirli parametreleri ayarlayarak geliştirilebilir. Bu parametrelerin ayarlanması son bölümde ele alınmıştır.

Şu şekilde gösterilen sorgu örnekleri, [GitHub deposunda](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)de yer alan [NYC TAXI seyahat verileri](https://chriswhong.com/open-data/foil_nyc_taxi/) senaryolarına özgüdür. Bu sorguların veri şeması zaten belirtilmiş ve çalıştırılmaya gönderilmeye hazırlanıyor. Son bölümde, Hive sorgularının performansının iyileşmesi için kullanıcıların ayarlayabilmesini sağlayan parametreler de ele alınmıştır.

Bu görev, [ekip veri bilimi işlemindeki (TDSP)](./index.yml)bir adımdır.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede sahip olduğunuz varsayılır:

* Bir Azure depolama hesabı oluşturuldu. Yönergelere ihtiyacınız varsa bkz. [Azure depolama hesabı oluşturma](../../storage/common/storage-account-create.md)
* HDInsight hizmeti ile özelleştirilmiş bir Hadoop kümesi sağlandı.  Yönergelere ihtiyacınız varsa bkz. [Gelişmiş analiz için Azure HDInsight Hadoop kümelerini özelleştirme](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Veriler Azure HDInsight Hadoop kümelerinde Hive tablolarına yüklendi. Bu yoksa, önce verileri Hive tablolarına yüklemek için [Create ve Hive tablolarına veri yükle](move-hive-tables.md) ' yi izleyin.
* Kümeye uzaktan erişim etkinleştirildi. Yönergelere ihtiyacınız varsa bkz. [Hadoop kümesinin baş düğümüne erişme](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Özellik oluşturma
Bu bölümde, Hive sorguları kullanılarak özelliklerin üretilebilme yollarına dair birkaç örnek açıklanmaktadır. Ek özellikler oluşturduktan sonra, bunları mevcut tabloya sütunlar olarak ekleyebilir veya ek özellikler ve birincil anahtar ile yeni bir tablo oluşturabilir ve bu da özgün tabloyla birlikte eklenebilir. Sunulan örnekler şunlardır:

1. [Sıklık tabanlı özellik oluşturma](#hive-frequencyfeature)
2. [Ikili sınıflandırmada kategorik değişkenlerin riskleri](#hive-riskfeature)
3. [Tarih saat alanından özellikleri Ayıkla](#hive-datefeatures)
4. [Metin alanından özellikleri Ayıkla](#hive-textfeatures)
5. [GPS koordinatları arasındaki mesafeyi hesaplama](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Sıklık tabanlı özellik oluşturma
Kategorik bir değişkenin seviyelerinin sıklıklarını veya birden çok kategorik değişkenden belirli düzey birleşimlerinin sıklıklarını hesaplamak için genellikle yararlıdır. Kullanıcılar bu frekansları hesaplamak için aşağıdaki betiği kullanabilir:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>İkili sınıflandırmada kategorik değişkenlerin riskleri
İkili sınıflandırmada, kullanılan modeller yalnızca sayısal özellikler alırken sayısal olmayan kategorik değişkenlerin sayısal özelliklere dönüştürülmesi gerekir. Bu dönüştürme, sayısal olmayan her düzey bir sayısal riskle değiştirilerek yapılır. Bu bölümde, kategorik bir değişkenin risk değerlerini (günlük ODD) hesaplayan bazı genel Hive sorguları gösterilmektedir.

```hiveql
set smooth_param1=1;
set smooth_param2=20;
select
    <column_name1>,<column_name2>,
    ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
from
    (
    select
        <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
    from
        (
        select
            <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
        from <databasename>.<tablename>
        )a
    group by <column_name1>, <column_name2>
    )b
```

Bu örnekte, değişkenleri `smooth_param1` ve `smooth_param2` verilerden hesaplanan risk değerlerini düzgünleştirmek üzere ayarlanır. Riskler arasında-INF ve INF arasında bir Aralık vardır. Bir risk > 0, hedefin 1 ' e eşit olduğu olasılığının 0,5 ' den fazla olduğunu gösterir.

Risk tablosu hesaplandıktan sonra, kullanıcılar risk tablosuna katılarak risk değerlerini bir tabloya atayabilir. Önceki bölümde Hive birleştirme sorgusu sağlandı.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Tarih saat alanlarından özellikleri Ayıkla
Hive, DateTime alanlarını işlemek için bir UDF kümesiyle gelir. Hive 'de, varsayılan tarih saat biçimi ' yyyy-aa-gg 00:00:00 ' (örneğin, ' 1970-01-01 12:21:32 ') şeklindedir. Bu bölümde, bir ayın gününü, bir tarih saat alanını ve varsayılan biçiminden farklı bir biçimde Tarih Saat dizesini varsayılan biçimde bir tarih saat dizesine dönüştüren diğer örnekleri gösteren örnekler gösterilmektedir.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

Bu Hive sorgusu, *\<datetime field>* varsayılan tarih saat biçiminde olduğunu varsayar.

Bir DateTime alanı varsayılan biçimde değilse, DateTime alanını önce UNIX zaman damgasına dönüştürmeniz ve sonra UNIX zaman damgasını varsayılan biçimde olan bir tarih saat dizesine dönüştürmeniz gerekir. DateTime varsayılan biçimde olduğunda, kullanıcılar, özellikleri ayıklamak için gömülü tarih saat UDF 'Leri uygulayabilir.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

Bu sorguda,, *\<datetime field>* *03/26/2015 12:04:39* gibi bir model varsa, *\<pattern of the datetime field> '* olması gerekir `'MM/dd/yyyy HH:mm:ss'` . Test etmek için kullanıcılar şunları çalıştırabilir

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

Bu sorgudaki *hivesampletable* , kümeler sağlandığında varsayılan olarak tüm Azure HDInsight Hadoop kümelerine önceden yüklenmiş olarak gelir.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Metin alanlarından özellikleri Ayıkla
Hive tablosunda boşluklarla ayrılmış bir sözcük dizesi içeren bir metin alanı olduğunda aşağıdaki sorgu, dizenin uzunluğunu ve dizedeki sözcüklerin sayısını ayıklar.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>GPS koordinatları kümeleri arasında uzaklıkları hesaplama
Bu bölümde verilen sorgu, NYC TAXI seyahat verilerine doğrudan uygulanabilir. Bu sorgunun amacı, özellikler oluşturmak için Hive içinde gömülü matematik işlevinin nasıl uygulanacağını gösterir.

Bu sorguda kullanılan alanlar, toplama *\_ boylam*, *toplama, \_ Enlem*, alt *\_ boylam* ve *açılan \_ Enlem* adlı toplama ve bırakma konumlarından oluşan GPS koordinatlarıdır. Toplama ve bırakma koordinatları arasındaki doğrudan mesafeyi hesaplayan sorgular şunlardır:

```hiveql
set R=3959;
set pi=radians(180);
select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
    *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
    *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
    +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
    pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxi.trip
where pickup_longitude between -90 and 0
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and 0
and dropoff_latitude between 30 and 90
limit 10;
```

İki GPS koordinatları arasındaki mesafeyi hesaplayan matematik denklemleri, Peter Lapisu tarafından yazılan <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">taşınabilir tür betikleri</a> sitesinde bulunabilir. Bu JavaScript 'te, işlev `toRad()` yalnızca Pi/180 *lat_or_lon*. Bu, Dereceyi radyana dönüştürür. Burada *lat_or_lon* Enlem veya boylam. Hive işlevi sağlamadığından, ancak işlevi sağladığından, `atan2` `atan` Işlev, `atan2` `atan` <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">vikovan</a>içinde sağlanan tanımı kullanarak yukarıdaki Hive sorgusunda işlevi tarafından uygulanır.

![Çalışma alanı oluşturma](./media/create-features-hive/atan2new.png)

Hive Embedded UDF 'Leri listesini <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>'Nin **yerleşik işlevler** bölümünde bulabilirsiniz.  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Gelişmiş Konular: sorgu hızını artırmak için Hive parametrelerini ayarlayın
Hive kümesinin varsayılan parametre ayarları Hive sorguları ve sorguların işlediği veriler için uygun olmayabilir. Bu bölümde, kullanıcıların Hive sorgularının performansını geliştirmek için ayarlayabileceği bazı parametreler açıklanmaktadır. Kullanıcıların, veri işleme sorgularından önce parametre ayarlama sorgularını eklemesi gerekir.

1. **Java yığın alanı**: büyük veri kümelerine katılmayı veya uzun kayıtları işlemeyi içeren sorgular için, **yığın alanı tükeniyor** ortak hatalardan biridir. *MapReduce. Map. Java. opts* ve *MapReduce. Task. IO. Sort. MB* parametreleri istenen değerlere ayarlanarak bu hata kaçınılabilir. Aşağıda bir örnek verilmiştir:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Bu parametre, Java yığın alanına 4 GB bellek ayırır ve ayrıca sıralama, daha fazla bellek ayırarak daha verimli hale gelir. Yığın alanıyla ilgili herhangi bir iş hatası hatası varsa, Bu ayırmalarla çalmak iyi bir fikirdir.

1. **DFS blok boyutu**: Bu parametre, dosya sisteminin depoladığı en küçük veri birimini ayarlar. Örnek olarak, DFS blok boyutu 128 MB ise, boyut ve 128 MB 'tan küçük olan tüm veriler tek bir blokta depolanır. 128 MB 'tan büyük verilere fazladan bloklar atanır. 
2. Küçük bir blok boyutu seçmek Hadoop 'ta büyük kafa başları oluşmasına neden olur. bu yana ad düğümü, dosyayla ilgili ilgili bloğu bulmak için birçok daha fazla isteği işlemek zorunda KALIDIR. Gigabayt (veya daha büyük) verilerle ilgilenirken önerilen bir ayar şunlardır:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. **Hive 'de birleştirme Işlemini En Iyi duruma getirme**: harita/küçültme çerçevesindeki birleştirme işlemleri tipik olarak azaltma aşamasında gerçekleşirken, bazen de büyük bir kazanç, eşleme aşamasında ("mapjoın" olarak da bilinir) zamanlama ile zaman ayırarak elde edilebilir. Bu seçeneği ayarlayın:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **Hive 'e Mapvı sayısını belirtme**: Hadoop, kullanıcının azaltıcının sayısını ayarlamasına izin veriyorsa, mapvana sayısı genellikle kullanıcı tarafından ayarlanmamalıdır. Bu sayı üzerinde bir ölçüde denetim kullanılmasına izin veren bir eli, her harita görevinin boyutu tarafından belirlendiği şekilde *mapred. min. Split. size* ve *mapred. Max. Split. size* Hadoop değişkenlerini seçmektedir:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Genellikle, varsayılan değeri:
    
   - *mapred. min. Split. size* değeri 0,
   - *mapred. Max. Split. size* **Long. Max** ve 
   - *DFS. Block. size* 64 MB 'tır.

     Görebileceğiniz gibi, veri boyutu verildiğinde, bu parametreleri "ayar" olarak ayarlamak, kullanılan mapas sayısını ayarlamamızı sağlar.

4. Hive performansını iyileştirmeye yönelik daha **Gelişmiş diğer seçenekler** aşağıda verilmiştir. Bu seçenekler, görevleri eşlemek ve azaltmak için ayrılan belleği ayarlamanıza olanak tanır ve performans için çok daha fazla performans sağlar. *MapReduce. azaltma. Memory. MB* ' nın, Hadoop kümesindeki her bir çalışan düğümünün fiziksel bellek boyutundan büyük olamaz.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```