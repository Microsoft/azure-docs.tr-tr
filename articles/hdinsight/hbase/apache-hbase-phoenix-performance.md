---
title: Azure HDInsight 'ta Phoenix performansı
description: Azure HDInsight kümeleri için Apache Phoenix performansını iyileştirmek için en iyi uygulamalar
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: b2a40802070510939332c3f5e876293445cf2df1
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810436"
---
# <a name="apache-phoenix-performance-best-practices"></a>Apache Phoenix performansı için en iyi yöntemler

[Apache Phoenix](https://phoenix.apache.org/) performansının en önemli yönü, temeldeki [Apache HBase](https://hbase.apache.org/)'i en iyi hale getirmenizi sağlar. Phoenix, SQL sorgularını taramalar gibi HBase işlemlerine dönüştüren üzerine HBase ilişkisel bir veri modeli oluşturur. Tablo şemanızın tasarımı, birincil anahtarınızda alanların seçimi ve sıralaması ve dizin kullanımı, Phoenix performansını etkiler.

## <a name="table-schema-design"></a>Tablo şeması tasarımı

Phoenix 'te bir tablo oluşturduğunuzda, bu tablo bir HBase tablosunda depolanır. HBase tablosu, birlikte erişilen sütun gruplarını (sütun aileleri) içerir. Phoenix tablosundaki bir satır, HBase tablosundaki, her satırın bir veya daha fazla sütunla ilişkili sürümlenmiş hücrelerden oluştuğu bir satırdır. Mantıksal olarak, tek bir HBase satırı, her biri aynı rowkey değerine sahip olan bir anahtar-değer çiftleri koleksiyonudur. Diğer bir deyişle, her anahtar-değer çiftinin bir rowkey özniteliği vardır ve bu rowkey özniteliğinin değeri belirli bir satır için aynıdır.

Bir Phoenix tablosunun şema tasarımı, birincil anahtar tasarımı, sütun ailesi tasarımı, tek sütun tasarımı ve verilerin nasıl bölümlenme olduğunu içerir.

### <a name="primary-key-design"></a>Birincil anahtar tasarımı

Phoenix 'teki bir tabloda tanımlanan birincil anahtar, verilerin temel alınan HBase tablosunun rowkey içinde nasıl depolandığını belirler. HBase 'de, belirli bir satıra erişmenin tek yolu rowkey kullanmaktır. Ayrıca, bir HBase tablosunda depolanan veriler rowkey tarafından sıralanır. Phoenix, satırdaki her bir sütunun değerini birincil anahtarda tanımlandıkları sırada birleştirerek rowkey değerini oluşturur.

Örneğin, kişiler için bir tablonun adı, soyadı, telefon numarası ve adres, hepsi de aynı sütun ailesinde bulunur. Artan sıra numarasına göre bir birincil anahtar tanımlayabilirsiniz:

|rowkey|       address|   telefon| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Oyji|

Bununla birlikte, genellikle lastName ile sorgulama yaptıysanız bu birincil anahtar iyi şekilde gerçekleştirilemeyebilir, çünkü her sorgu her lastName değerini okumak için tam tablo taraması gerektirir. Bunun yerine, lastName, firstName ve sosyal güvenlik numarası sütunlarında bir birincil anahtar tanımlayabilirsiniz. Bu son sütun, babalar ve son gibi aynı adreste bulunan iki sakın belirsizliğini ortadan kaldırmaya yönelik bir addır.

|rowkey|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Oyji| 222 |

Bu yeni birincil anahtarla, Phoenix tarafından oluşturulan satır anahtarları şöyle olacaktır:

|rowkey|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Çüji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Oyji| 222 |

Yukarıdaki ilk satırda, rowkey verileri gösterilen şekilde gösterilir:

|rowkey|       key|   value| 
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|telefon |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

Bu rowkey artık verilerin yinelenen bir kopyasını depolar. Bu değer, temel alınan HBase tablosundaki her hücreye dahil edildiğinden, birincil anahtarınıza eklediğiniz sütunların boyutunu ve sayısını göz önünde bulundurun.

Ayrıca, birincil anahtarda tek tek artan değerler varsa, yazma etkin noktaları oluşturmamaya yardımcı olmak için tablo, *anahtar demetleri* ile oluşturmanız gerekir. [bölüm verilerine](#partition-data)bakın.

### <a name="column-family-design"></a>Sütun ailesi tasarımı

Bazı sütunlara diğerlerinden daha sık erişiliyorsa, sık erişilen sütunları nadiren erişilen sütunlardan ayırmak için birden çok sütunlu aileleri oluşturmanız gerekir.

Ayrıca, belirli sütunlara birlikte erişiliyorsa, bu sütunları aynı sütun ailesine koyun.

### <a name="column-design"></a>Sütun tasarımı

* Büyük sütunların g/ç maliyetlerine bağlı olarak, VARCHAR sütunlarını yaklaşık 1 MB 'tan tutun. Sorgular işlenirken, HBase, hücreleri istemciye göndermeden önce tam olarak bir şekilde çıkarır ve istemci bunları uygulama koduna teslim etmeden önce tam olarak alır.
* Sütun değerlerini protoarabelleğe, avro, msgpack veya BSON gibi bir kompakt biçim kullanarak depolayın. JSON, daha büyük olduğu için önerilmez.
* Gecikme süresini ve g/ç maliyetlerini kesmek için depolamadan önce verileri sıkıştırmayı göz önünde bulundurun.

### <a name="partition-data"></a>Verileri bölümleme

Phoenix, verilerinizin dağıtıldığı bölge sayısını denetlemenizi sağlar ve bu da okuma/yazma performansını önemli ölçüde artırabilir. Bir Phoenix tablosu oluştururken, verilerinizi güvenlik altına alabilir veya önceden bölebilirsiniz.

Oluşturma sırasında bir tabloyu oluşturmak için, anahtar demetlerinin sayısını belirtin:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Bu, tablo, verileri otomatik olarak seçerek birincil anahtarların değerleri üzerinde böler. 

Tablonun nerede bölündüğünü denetlemek için bölmenin gerçekleştiği Aralık değerlerini sağlayarak tabloyu önceden bölebilirsiniz. Örneğin, üç bölgenin üzerinde bölünen bir tablo oluşturmak için:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Dizin tasarımı

Phoenix dizini, dizinlenmiş tablodaki verilerin bir kısmının veya tümünün kopyasını depolayan bir HBase tablosudur. Dizin, belirli sorgu türleri için performansı geliştirir.

Tanımlanmış birden fazla dizininiz varsa ve sonra bir tablo sorgulayıp, Phoenix otomatik olarak sorgunun en iyi dizinini seçer. Birincil dizin, seçtiğiniz birincil anahtarlara göre otomatik olarak oluşturulur.

Beklenen sorgular için, sütunlarını belirterek ikincil dizinler de oluşturabilirsiniz.

Dizinlerinizi tasarlarken:

* Yalnızca ihtiyacınız olan dizinleri oluşturun.
* Sık güncellenen tablolardaki dizinlerin sayısını sınırlayın. Bir tablodaki güncelleştirmeler, hem ana tablo hem de dizin tablolarına yazma olarak dönüştürülür.

## <a name="create-secondary-indexes"></a>İkincil dizinler oluştur

İkincil dizinler, depolama alanı ve yazma hızı maliyetinde bir nokta aramasına tam tablo taraması olacağını açıp okuma performansını iyileştirebilirler. İkincil dizinler tablo oluşturulduktan sonra eklenebilir veya kaldırılabilir, mevcut sorgularda değişiklik gerektirmez – sorgular yalnızca daha hızlı çalışır. Gereksinimlerinize bağlı olarak, kapsanan dizinler, işlevsel dizinler veya her ikisini de oluşturmayı düşünün.

### <a name="use-covered-indexes"></a>Kapsanan dizinleri kullan

Kapsanan dizinler, dizinli değerlere ek olarak satırdaki verileri içeren dizinlerdir. İstenen dizin girişini bulduktan sonra, birincil tabloya erişmeniz gerekmez.

Örneğin, örnek kişi tablosunda yalnızca socialSecurityNum sütununda ikincil bir dizin oluşturabilirsiniz. Bu ikincil dizin, socialSecurityNum değerlerine göre filtreleyen sorguları hızlandıracaktır, ancak diğer alan değerlerinin alınması ana tabloya karşı başka bir okuma gerektirir.

|rowkey|       address|   telefon| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Çüji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Oyji| 222 |

Bununla birlikte, genel olarak, socialSecurityNum verilen firstName ve lastName bilgilerini aramak istiyorsanız, Dizin tablosunda gerçek veriler olarak firstName ve lastName içeren bir kapsanan dizin oluşturabilirsiniz:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Bu kapsanan Dizin, aşağıdaki sorgunun tüm verileri yalnızca ikincil dizini içeren tablodan okuyarak almasını sağlar:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>İşlevsel dizinleri kullanma

İşlevsel dizinler, sorgularda kullanılmasını beklediğinizi rastgele bir ifadede dizin oluşturmanıza imkan tanır. İşlevsel bir dizin oluşturulduktan ve bir sorgu bu ifadeyi kullandığında, dizin veri tablosu yerine sonuçları almak için kullanılabilir.

Örneğin, bir kişinin Birleşik adı ve soyadı için büyük/küçük harfe duyarsız aramalar yapmanıza olanak sağlayan bir dizin oluşturabilirsiniz:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Sorgu tasarımı

Sorgu tasarımında başlıca noktalar şunlardır:

* Sorgu planını anlayın ve beklenen davranışını doğrulayın.
* Verimli bir şekilde birleştirin.

### <a name="understand-the-query-plan"></a>Sorgu planını anlama

[Sqlline](http://sqlline.sourceforge.net/)Içinde, Phoenix 'in gerçekleştireceği işlem planını görüntülemek için SQL SORGUNUZUN ardından açıkla ' yı kullanın. Planı denetleyin:

* Uygun durumlarda birincil anahtarınızı kullanır.
* Veri tablosu yerine uygun ikincil dizinleri kullanır.
* , Tablo TARAMASı yerine Aralık taramasını kullanır veya mümkün olduğunda taramayı atlar.

#### <a name="plan-examples"></a>Plan örnekleri

Örnek olarak, Uçuş gecikmesi bilgilerini depolayan FıŞıKLARı adlı bir tablonuz olduğunu varsayalım.

Airlineıd ile tüm fışıklardan birini `19805`seçmek için airlineıd, birincil anahtarda veya herhangi bir dizinde olmayan bir alandır:

    select * from "FLIGHTS" where airlineid = '19805';

Açıkla komutunu aşağıdaki gibi çalıştırın:

    explain select * from "FLIGHTS" where airlineid = '19805';

Sorgu planı şöyle görünür:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Bu planda, FıŞıKLARA göre tam tarama tümceciğini ifade edin. Bu tümcecik, yürütmenin, daha verimli Aralık TARAMASı veya tarama atlama seçeneği yerine tablodaki tüm satırların üzerinde tarama yaptığını gösterir.

Şimdi, flightnum 'ın 1 ' den büyük olduğu taşıyıcı `AA` için 2 Ocak 2014 ' de fışıkları sorgulamak istediğinizi varsayalım. Yıl, ay, dayofmonth, taşıyıcı ve flightnum sütunlarının örnek tabloda bulunduğunu ve bileşik birincil anahtarın bir parçası olduğunu varsayalım. Sorgu şöyle görünür:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Bu sorgunun planını şu şekilde incelim:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Elde edilen plan:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Köşeli ayraçlar içindeki değerler, birincil anahtarların değer aralığıdır. Bu durumda, Aralık değerleri 2014, Month 1 ve dayofmonth 2 ile düzeltilir, ancak 2 ve üzerinde (`*`) başlayan flightnum değerlerine izin verir. Bu sorgu planı, birincil anahtarın beklenen şekilde kullanıldığını onaylar.

Ardından, fışıkları tablosunda yalnızca taşıyıcı alanında olan adlı `carrier2_idx` bir dizin oluşturun. Bu dizin Ayrıca, verileri dizinde depolanan kapsanan sütunlar olarak da flightdate,,,, Origin ve flightnum bilgilerini içerir.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Aşağıdaki sorguda olduğu gibi, flightdate ve ıdinum ile birlikte taşıyıcıyı almak istediğinizi varsayalım:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Kullanılmakta olan dizini görmeniz gerekir:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Plan sonuçlarını açıkla bölümünde görünebilen öğelerin tamamı listesi için [Apache Phoenix ayarlama kılavuzundaki](https://phoenix.apache.org/tuning_guide.html)açıkla planları bölümüne bakın.

### <a name="join-efficiently"></a>Verimli bir şekilde birleştirin

Genellikle, tek bir kenar küçük olmadığı ve özellikle sık sorgularda, birleşimlerden kaçınmak istersiniz.

Gerekirse, `/*+ USE_SORT_MERGE_JOIN */` ipucu ile büyük birleşimler yapabilirsiniz, ancak büyük bir birleştirme çok sayıda satır üzerinde pahalı bir işlemdir. Tüm sağ taraftaki tabloların genel boyutu kullanılabilir belleği aşarsa, `/*+ NO_STAR_JOIN */` ipucunu kullanın.

## <a name="scenarios"></a>Senaryolar

Aşağıdaki kılavuzlar bazı yaygın desenleri anlatmaktadır.

### <a name="read-heavy-workloads"></a>Okuma ağır iş yükleri

Okuma ağır kullanım örnekleri için, dizinleri kullandığınızdan emin olun. Ayrıca, okuma zamanı ek yükünü kaydetmek için kapsanan dizinler oluşturmayı göz önünde bulundurun.

### <a name="write-heavy-workloads"></a>Yazma ağır iş yükleri

Birincil anahtarın tek bir şekilde artdığı, yazma ağır iş yükleri için, gereken ek taramalar nedeniyle genel okuma aktarım hızı masrafında yazma etkin noktalarına karşı, yazma etkin noktalarına karşı, anahtar demetleri oluşturun. Ayrıca, çok sayıda kayıt yazmak için UPSERT kullanırken, oto yürütmeyi kapatın ve kayıtları toplu olarak uygulayın.

### <a name="bulk-deletes"></a>Toplu silmeler

Büyük bir veri kümesini silerken, SILME sorgusunu vermeden önce, istemci silinen tüm satırların satır anahtarlarını anımsaması gerekmiyorsa, tekrar yürütme özelliğini etkinleştirin. Yeniden yürütme, istemcinin SILME işleminden etkilenen satırları arabelleğe almasını engeller. bu sayede, Phoenix 'in onları istemciye döndürme masrafı olmadan doğrudan bölge sunucularında silmesine izin vermez.

### <a name="immutable-and-append-only"></a>Sabit ve salt ekleme

Senaryonuz veri bütünlüğü üzerinde yazma hızına tercih eder, tablolarınızı oluştururken yazma ön günlüğünü devre dışı bırakmayı göz önünde bulundurun:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Bu ve diğer seçeneklerle ilgili ayrıntılı bilgi için bkz. [Apache Phoenix dilbilgisi](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Phoenix ayarlama Kılavuzu](https://phoenix.apache.org/tuning_guide.html)
* [İkincil dizinler](https://phoenix.apache.org/secondary_indexing.html)
