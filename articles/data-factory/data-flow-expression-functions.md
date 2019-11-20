---
title: Azure Data Factory veri akışı eşleme özelliğindeki ifade işlevleri
description: Eşleme veri akışındaki ifade işlevleri hakkında bilgi edinin.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: dc742fc625604e71909f49c7453a9215dce71e35
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596973"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Eşleme veri akışındaki veri dönüştürme ifadeleri 



## <a name="expression-functions"></a>İfade işlevleri

Data Factory ' de, veri dönüşümlerini yapılandırmak için veri akışını eşleme özelliğinin ifade dilini kullanın.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Bir sayının mutlak değeri.

* ``abs(-20) -> 20``

* ``abs(10) -> 10``

___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Kosinüs ters değerini hesaplar

* ``acos(1) -> 0.0``

___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Bir çift dize veya sayı ekler. Gün sayısı için bir tarih ekler. Zaman damgasına bir süre ekler. Benzer türdeki bir diziyi diğerine ekler. \+ İşleciyle aynı

* ``add(10, 20) -> 30``

* ``10 + 20 -> 30``

* ``add('ice', 'cream') -> 'icecream'``

* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``

* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``

* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``

* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``

* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``

___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Tarih veya zaman damgasına gün ekleyin. Tarih için + işleciyle aynı

* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``

___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Tarih veya zaman damgasına aylar ekleyin. İsteğe bağlı olarak bir saat dilimi geçirebilirsiniz

* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``

* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``

___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Mantıksal AND işleci. & İle aynı &

* ``and(true, false) -> false``

* ``true && false -> false``

___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ters Sinüs değerini hesaplar

* ``asin(0) -> 0.0``

___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ters tanjant değerini hesaplar

* ``atan(0) -> 0.0``

___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Düzlemin pozitif x ekseni ile koordinatlar tarafından verilen nokta arasındaki radyan cinsinden açıyı döndürür

* ``atan2(0, 0) -> 0.0``

___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Bir sütunun değerlerinin ortalamasını alır

* ``avg(sales)``

___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ölçütlere dayalı olarak bir sütunun değerlerinin ortalaması alınır

* ``avgIf(region == 'West', sales)``

___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Akıştaki ada göre bir sütun değeri seçer. İkinci bağımsız değişken olarak isteğe bağlı bir akış adı geçirebilirsiniz. Birden çok eşleşme varsa, ilk eşleşme döndürülür. Eğer eşleşme yoksa NULL değer döndürür. Döndürülen değer tür dönüştürme işlevlerinden biri tarafından dönüştürülmüş olmalıdır (TO_DATE, TO_STRING...).  Tasarım zamanında bilinen sütun adları yalnızca adına göre değinmelidir. Hesaplanan girişler desteklenmez, ancak parametre değiştirmeler kullanabilirsiniz

* ``toString(byName('parent'))``

* ``toLong(byName('income'))``

* ``toBoolean(byName('foster'))``

* ``toLong(byName($debtCol))``

* ``toString(byName('Bogus Column'))``

* ``toString(byName('Bogus Column', 'DeriveStream'))``

___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Akışta göreli konumuna göre (1 tabanlı) bir sütun değeri seçer. Konum sınırların dışında ise, NULL bir değer döndürür. Döndürülen değer tür dönüştürme işlevlerinden biri tarafından dönüştürülmüş olmalıdır (TO_DATE, TO_STRING...) Hesaplanan girişler desteklenmez, ancak parametre değiştirmeler kullanabilirsiniz

* ``toString(byPosition(1))``

* ``toDecimal(byPosition(2), 10, 2)``

* ``toBoolean(byName(4))``

* ``toString(byName($colName))``

* ``toString(byPosition(1234))``

___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Değişen koşullara göre bir değer veya diğeri geçerlidir. Giriş sayısı çiftse, diğeri son koşul için varsayılan olarak NULL olur

* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``

* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``

* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``

* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``

___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sayının Küp kökünü hesapla

* ``cbrt(8) -> 2.0``

___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Sayıdan küçük olan en küçük tamsayıyı döndürür

* ``ceil(-0.1) -> 0``

___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Giriş kümesinden gelen ilk null değeri döndürür. Tüm girişler aynı türde olmalıdır

* ``coalesce(10, 20) -> 10``

* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Aynı türdeki iki değeri karşılaştırır. Değer1 < değer2 ise negatif tamsayı döndürür, 0 if Değer1 = = değer2 ise pozitif değer, Eğer değer1 > değer2

* ``(compare(12, 24) < 1) -> true``

* ``(compare('dumbo', 'dum') > 0) -> true``

___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Değişken sayıda dizeyi birlikte birleştirir. Dizelerle + işleciyle aynı

* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``

* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``

* ``isNull('sql' + null) -> true``

___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Bir dizi dizeyi bir ayırıcıyla birlikte birleştirir. İlk parametre ayırıcıdır

* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``

* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``

* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``

___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Belirtilen dizideki herhangi bir öğe, belirtilen koşulda doğru olarak değerlendiriliyorsa true değerini döndürür. Contains, koşul işlevindeki bir öğeye başvuru bekliyor #item

* ``contains([1, 2, 3, 4], #item == 3) -> true``

* ``contains([1, 2, 3, 4], #item > 5) -> false``

___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir kosinüs değeri hesaplar

* ``cos(10) -> -0.8390715290764524``

___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir değerin hiperbolik kosinüsünü hesaplar

* ``cosh(0) -> 1.0``

___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Değerlerin toplam sayısını alır. İsteğe bağlı sütunlar belirtilmişse, sayımla NULL değerleri yoksayar

* ``count(custId)``

* ``count(custId, custName)``

* ``count()``

* ``count(iif(isNull(custId), 1, NULL))``

___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Bir sütun kümesinin farklı değerlerinin toplam sayısını alır

* ``countDistinct(custId, custName)``

___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Ölçütlere göre toplam değer sayısını alır. İsteğe bağlı sütun belirtilmişse, sayımla NULL değerleri yoksayar

* ``countIf(state == 'CA' && commission < 10000, name)``

___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
İki sütun arasındaki popülasyon kovaryansını alır

* ``covariancePopulation(sales, profit)``

___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre iki sütunun popülasyon kovaryansını alır

* ``covariancePopulationIf(region == 'West', sales)``

___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
İki sütunun örnek kovaryansını alır

* ``covarianceSample(sales, profit)``

___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, iki sütunun örnek kovaryansını alır

* ``covarianceSampleIf(region == 'West', sales, profit)``

___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Yalnızca 0 (256), 224, 256, 384, 512 değerlerinin yer aldığı bir bit uzunluğu verilen, farklı ilkel veri türlerinin sütun kümesinin CRC32 karmasını hesaplar. Bir satır parmak izini hesaplamak için kullanılabilir

* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``

___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist işlevi, bir değerin bölüm içindeki tüm değerlere göre konumunu hesaplar. Sonuç, Bölüm sıralamasına göre geçerli satıra göre veya buna eşit olan satır sayısıdır ve pencere bölümündeki toplam satır sayısı ile ayrılır. Sıralamada bulunan tüm bağlama değerleri aynı konuma göre değerlendirilir.

* ``cumeDist()``

___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Bu işin çalışmaya başladığı geçerli tarihi alır. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Yerel Saat dilimi varsayılan olarak kullanılır. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``currentDate() == toDate('2250-12-31') -> false``

* ``currentDate('PST')  == toDate('2250-12-31') -> false``

* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``

___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
İş yerel saat dilimiyle çalışmaya başladığında geçerli zaman damgasını alır

* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``

___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>

Geçerli zaman damgasını UTC olarak alır. Geçerli saatin, küme saat diliminizdeki farklı bir saat diliminde yorumlanması istiyorsanız, isteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Varsayılan olarak geçerli saat dilimine ayarlanır. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. UTC saatini farklı bir saat dilimine dönüştürmek için [SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) kullanın, fromUTC () kullanın.

* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``

* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``

* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``

___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Tarih verilen tarihin gününü alır

* ``dayOfMonth(toDate('2018-06-08')) -> 8``

___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Bir tarih verilen haftanın gününü alır. 1-Pazar, 2-Pazartesi..., 7-Cumartesi

* ``dayOfWeek(toDate('2018-06-08')) -> 6``

___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Tarih verilen yılın gününü alır

* ``dayOfYear(toDate('2016-04-09')) -> 100``

___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Gün sayısı için milisaniye cinsinden süre

* ``days(2) -> 172800000L``

___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Radyana derece derece dönüştürür

* ``degrees(3.141592653589793) -> 180``

___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Bir değer grubundaki bir değerin derecesini hesaplar. Sonuç, Bölüm sıralamasına göre geçerli satırdan önceki veya eşit olan satır sayısıdır. Değerler dizide boşluk üretmeyecektir. Yoğun sıra, veriler sıralanmasa bile ve değerlerde değişiklik ararken bile çalışıyor

* ``denseRank(salesQtr, salesAmt)``

___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Sayı çiftini böler. /İşleciyle aynı

* ``divide(20, 10) -> 2``

* ``20 / 10 -> 2``

___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Dizenin belirtilen dize ile bitip bitmediğini denetler

* ``endsWith('dumbo', 'mbo') -> true``

___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Karşılaştırma eşittir işleci. = = İşleci ile aynı

* ``equals(12, 24) -> false``

* ``12 == 24 -> false``

* ``'bad' == 'bad' -> true``

* ``isNull('good' == toString(null)) -> true``

* ``isNull(null == null) -> true``

___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Karşılaştırma eşittir işleci büyük/küçük harf yok sayılıyor. < = > İşleci ile aynı

* ``'abc'<=>'Abc' -> true``

* ``equalsIgnoreCase('abc', 'Abc') -> true``

___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Bir sayının faktöriyelini hesapla

* ``factorial(5) -> 120``

___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Her zaman yanlış bir değer döndürür. ' False ' adlı bir sütun varsa işlev sözdizimini (false ()) kullanın

* ``(10 + 20 > 30) -> false``

* ``(10 + 20 > 30) -> false()``

___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Öğeleri, belirtilen koşulu karşılamayan dizinin dışına filtreler. Filtre, koşul işlevindeki bir öğeye başvuru bekliyor #item

* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``

* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``

___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Bir sütun grubunun ilk değerini alır. IgnoreNulls ikinci parametresi atlanırsa, false olarak kabul edilir

* ``first(sales)``

* ``first(sales, false)``

___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Sayıdan büyük olan en büyük tamsayıyı döndürür

* ``floor(-0.1) -> -1``

___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
UTC 'den zaman damgasına dönüştürür. İsteğe bağlı olarak, saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Kullanılabilir biçimler için geçerli timezoneRefer Java 'nın SimpleDateFormat öğesine varsayılan olarak ayarlanır. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``

* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``

___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Karşılaştırma daha büyük işleci. > İşleçle aynı

* ``greater(12, 24) -> false``

* ``('dumbo' > 'dum') -> true``

* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd 
HH:mm:ss.SSS')) -> true``

___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Karşılaştırma daha büyük veya eşit işleç. > = İşleci ile aynı

* ``greaterOrEqual(12, 12) -> true``

* ``('dumbo' >= 'dum') -> true``

___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Giriş null değerlerini atlayarak değer listesi arasındaki en büyük değeri döndürür. Tüm girişler null ise null döndürür

* ``greatest(10, 30, 15, 20) -> 30``

* ``greatest(10, toInteger(null), 20) -> 20``

* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``

* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``

___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Akıştaki ada göre bir sütun değeri olup olmadığını denetler. İkinci bağımsız değişken olarak isteğe bağlı bir akış adı geçirebilirsiniz.  Tasarım zamanında bilinen sütun adları yalnızca adına göre değinmelidir. Hesaplanan girişler desteklenmez, ancak parametre değiştirmeler kullanabilirsiniz

* ``hasColumn('parent')``

___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Bir zaman damgasının saat değerini alır. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Yerel Saat dilimi varsayılan olarak kullanılır. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``

* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``

___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Saat sayısı için milisaniye cinsinden süre

* ``hours(2) -> 7200000L``

___
### <code>iMap</code>
<code><b>iMap(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Dizideki her öğeyi, belirtilen ifadeyi kullanarak yeni bir öğeye eşler. Map, #item ifade işlevindeki bir öğeye bir başvuru ve öğe dizinine #index olarak bir başvuru bekliyor

* ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``

___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Bir koşula bağlı olarak bir değer veya diğerini uygular. Diğeri belirtilmemişse NULL kabul edilir. Her iki değer de uyumlu olmalıdır (sayısal, dize...)

* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``

* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``

* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``

___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Değerin NULL olup olmadığını denetler ve diğerini döndürür. İlk null olmayan değeri bulana kadar tüm girişleri sınar

* ``iifNull(10, 20) -> 10``

* ``iifNull(null, 20, 40) -> 20``

* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``

___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Dizide bir öğe olup olmadığını denetler

* ``in([10, 20, 30], 10) -> true``

* ``in(['good', 'kid'], 'bad') -> false``

___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Her sözcüğün ilk harfini büyük harfe dönüştürür. Sözcükler boşluk ile ayrılmış olarak tanımlanır

* ``initCap('cool iceCREAM') -> 'Cool Icecream'``

___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Bir dize içindeki alt dizenin konumunu (1 tabanlı) bulur. 0 bulunamazsa döndürülür

* ``instr('dumbo', 'mbo') -> 3``

* ``instr('microsoft', 'o') -> 5``

* ``instr('good', 'bad') -> 0``

___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Satırın silme için işaretlenip işaretlenmediğini denetler. Birden fazla giriş akışı alan dönüşümler için akışın (1 tabanlı) dizinini geçirebilirsiniz. Akış dizini için varsayılan değer 1 ' dir

* ``isDelete()``

* ``isDelete(1)``

___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Satırın hata olarak işaretlenip işaretlenmediğini denetler. Birden fazla giriş akışı alan dönüşümler için akışın (1 tabanlı) dizinini geçirebilirsiniz. Akış dizini için varsayılan değer 1 ' dir

* ``isError()``

* ``isError(1)``

___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Satırın yok sayılacak şekilde işaretlenip işaretlenmediğini denetler. Birden fazla giriş akışı alan dönüşümler için akışın (1 tabanlı) dizinini geçirebilirsiniz. Akış dizini için varsayılan değer 1 ' dir

* ``isIgnore()``

* ``isIgnore(1)``

___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Satırın ekleme için işaretlenip işaretlenmediğini denetler. Birden fazla giriş akışı alan dönüşümler için akışın (1 tabanlı) dizinini geçirebilirsiniz. Akış dizini için varsayılan değer 1 ' dir

* ``isInsert()``

* ``isInsert(1)``

___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Arama sırasında satırın eşleşip eşleşmediğini denetler. Birden fazla giriş akışı alan dönüşümler için akışın (1 tabanlı) dizinini geçirebilirsiniz. Akış dizini için varsayılan değer 1 ' dir

* ``isMatch()``

* ``isMatch(1)``

___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Değerin NULL olup olmadığını denetler

* ``isNull(NULL()) -> true``

* ``isNull('') -> false``

___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Satırın güncelleştirme için işaretlenip işaretlenmediğini denetler. Birden fazla giriş akışı alan dönüşümler için akışın (1 tabanlı) dizinini geçirebilirsiniz. Akış dizini için varsayılan değer 1 ' dir

* ``isUpdate()``

* ``isUpdate(1)``

___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Satırın ekleme için işaretlenip işaretlenmediğini denetler. Birden fazla giriş akışı alan dönüşümler için akışın (1 tabanlı) dizinini geçirebilirsiniz. Akış dizini için varsayılan değer 1 ' dir

* ``isUpsert()``

* ``isUpsert(1)``

___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun basıklığını alır

* ``kurtosis(sales)``

___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, bir sütunun basıklığını alır

* ``kurtosisIf(region == 'West', sales)``

___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Geçerli satırdan önce değerlendirilen n satıra ilk parametre değerini alır. İkinci parametre, geri aranacak satır sayısıdır ve varsayılan değer 1 ' dir. Çok sayıda satır yoksa varsayılan değer belirtilmediği sürece null değeri döndürülür

* ``lag(amount, 2)``

* ``lag(amount, 2000, 100)``

___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Bir sütun grubunun son değerini alır. IgnoreNulls ikinci parametresi atlanırsa, false olarak kabul edilir

* ``last(sales)``

* ``last(sales, false)``

___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Tarih verilen ayın son tarihini alır

* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``

___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Geçerli satırdan sonra değerlendirilen n satıra ilk parametre değerini alır. İkinci parametre, görüntülenecek satır sayısı ve varsayılan değer 1 ' dir. Çok sayıda satır yoksa varsayılan değer belirtilmediği sürece null değeri döndürülür

* ``lead(amount, 2)``

* ``lead(amount, 2000, 100)``

___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Karşılaştırma küçüktür veya eşittir işleci. @No__t_0 ile aynı

* ``least(10, 30, 15, 20) -> 10``

* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``

___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Dizin 1 ' den karakter sayısı ile başlayan bir alt dize ayıklar. Alt DIZEYLE aynı (Str, 1, n)

* ``left('bojjus', 2) -> 'bo'``

* ``left('bojjus', 20) -> 'bojjus'``

___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Dizenin uzunluğunu döndürür

* ``length('dumbo') -> 5``

___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Karşılaştırma daha az işleci. < İşleçle aynı

* ``lesser(12, 24) -> true``

* ``('abcd' < 'abc') -> false``

* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``

___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Karşılaştırma küçüktür veya eşittir işleci. @No__t_0 ile aynı

* ``lesserOrEqual(12, 12) -> true``

* ``('dumbo' <= 'dum') -> false``

___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
İki dize arasındaki mesafeyi alır

* ``levenshtein('boys', 'girls') -> 4``

___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Bu, tam anlamıyla eşleşen bir dizedir. Özel durumlar aşağıdaki özel sembollerdir: _, girişte bir karakter ile eşleşir (şuna benzer. POSIX normal ifadelerinde)%, girişte sıfır veya daha fazla karakterle eşleşir (POSIX normal ifadelerinde. * ile benzerdir).
Kaçış karakteri ' '. Bir kaçış karakteri özel bir sembolden veya başka bir kaçış karakteriyle önceyse, aşağıdaki karakter tam anlamıyla eşleştirilir. Başka bir karakter kaçış geçersizdir.

* ``like('icecream', 'ice%') -> true``

___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Belirli bir konumu Başlatan bir dize içindeki alt dizenin konumunu (1 tabanlı) bulur. Konum atlanırsa, dizenin başından hesaba göre değerlendirilir. 0 bulunamazsa döndürülür

* ``locate('mbo', 'dumbo') -> 3``

* ``locate('o', 'microsoft', 6) -> 7``

* ``locate('bad', 'good') -> 0``

___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Günlük değerini hesaplar. İsteğe bağlı bir taban, kullanıldıysa bir Euler numarası sağlanabilir

* ``log(100, 10) -> 2``

___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
10 tabanına göre günlük değerini hesaplar

* ``log10(100) -> 2``

___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Küçük harf, bir dize

* ``lower('GunChus') -> 'gunchus'``

___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Belirli bir uzunluğa ulaşana kadar dizeyi sağlanan doldurmaya göre aşağı doğru doldurma. Dize uzunluğuna eşit veya daha büyük ise, bu durumda uzunluğa atılır

* ``lpad('dumbo', 10, '-') -> '-----dumbo'``

* ``lpad('dumbo', 4, '-') -> 'dumb'``

* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``

___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Sol karakterlerin bir dizesini sola kırpar. İkinci parametre belirtilmemişse boşluğu kırpar. Aksi takdirde, ikinci parametrede belirtilen karakterleri kırpar

* ``ltrim('  dumbo  ') -> 'dumbo  '``

* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``

___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Dizideki her öğeyi, belirtilen ifadeyi kullanarak yeni bir öğeye eşler. Eşleme, ifade işlevindeki bir öğeye bir başvuru bekliyor #item

* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``

* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``

___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Bir sütunun en büyük değerini alır

* ``max(sales)``

___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ölçütlere göre, bir sütunun en büyük değerini alır

* ``maxIf(region == 'West', sales)``

___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Farklı ilkel veri türlerinin sütun kümesinin MD5 özetini hesaplar ve 32 karakter onaltılı dize döndürür. Bir satır parmak izini hesaplamak için kullanılabilir

* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``

___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Bir sütunun değerlerinin ortalama değerini alır. AVG ile aynı

* ``mean(sales)``

___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ölçütlere dayalı olarak bir sütunun değerlerinin ortalaması alınır. AvgIf ile aynı

* ``meanIf(region == 'West', sales)``

___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Bir tarihin milisaniyelik değerini alır. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Yerel Saat dilimi varsayılan olarak kullanılır. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``

___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Milisaniye cinsinden milisaniye cinsinden süre

* ``seconds(2) -> 2L``

___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Bir sütunun en küçük değerini alır

* ``min(sales)``

___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ölçütlere göre, bir sütunun en küçük değerini alır

* ``minIf(region == 'West', sales)``

___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Sayıları çıkartır. Gün sayısından çıkar. Zaman damgasından alt katman süresi. Milisaniye cinsinden farkı almak için iki zaman damgasını alt katman. -İşleci ile aynı

* ``minus(20, 10) -> 10``

* ``20 - 10 -> 10``

* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``

* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``

* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``

* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``

___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Bir zaman damgasının dakika değerini alır. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Yerel Saat dilimi varsayılan olarak kullanılır. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``

* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``

___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Milisaniye cinsinden dakika sayısı

* ``minutes(2) -> 120000L``

___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Sayı çiftinin mod sayısı. % İşleci ile aynı

* ``mod(20, 8) -> 4``

* ``20 % 8 -> 4``

___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Tarih veya zaman damgasının ay değerini alır

* ``month(toDate('2012-8-8')) -> 8``

___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
İki tarih arasındaki ay sayısını alır. Hesaplamayı yuvarlayaseçebilirsiniz. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Yerel Saat dilimi varsayılan olarak kullanılır. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Sayı çiftini çarpar. \* İşleciyle aynı

* ``multiply(20, 10) -> 200``

* ``20 * 10 -> 200``

___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
NTile işlevi her pencere bölümü için satırları, 1 ' den en `n` ' e kadar olan `n` demetlerine böler. Demet değerleri en fazla 1 farklı olacaktır. Bölümdeki satır sayısı demet sayısına eşit olarak bölünmezse, ilk sepete başlayarak, geri kalan değerler her demet için bir dağıtılır. NTile işlevi, terkutucukların, kubotların ve diğer yaygın Özet istatistiğin hesaplanması için yararlıdır. İşlev, başlatma sırasında iki değişkeni hesaplar: düzenli bir demet boyutunun boyutuna eklenmiş bir ek satır olacaktır. Her iki değişken de geçerli bölümün boyutunu temel alır. Hesaplama işlemi sırasında işlev geçerli satır numarasını, geçerli demet numarasını ve demet 'in değiştirileceği satır numarasını (bucketThreshold) izler. Geçerli satır numarası demet eşiğine ulaştığında, demet değeri bir artırılır ve eşik, demet boyutu (ve geçerli demet doldurulmuş ise bir ek) ile artırılır.

* ``nTile()``

* ``nTile(numOfBuckets)``

___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Bir sayıyı geçersiz kılar. Pozitif sayıları negatif ve tam tersi yönde döndürür

* ``negate(13) -> -13``

___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Sonraki benzersiz diziyi döndürür. Numara yalnızca bir bölüm içinde ardışık ve PartitionID tarafından önekli

* ``nextSequence() == 12313112 -> false``

___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Dize değerini, aksanlı Unicode karakterlerini ayırmak için normalleştirin

* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``

___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Mantıksal Değilleme İşleci

* ``not(true) -> false``

* ``not(10 == 20) -> true``

___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Karşılaştırma eşit değildir işleci. ! = İşleci ile aynı

* ``12 != 24 -> true``

* ``'bojjus' != 'bo' + 'jjus' -> false``

___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL değeri döndürür. ' Null ' adlı bir sütun varsa işlev sözdizimini (null ()) kullanın. Tarafından kullanılan tüm işlemler NULL ile sonuçlanır

* ``isNull('dumbo' + null) -> true``

* ``isNull(10 * null) -> true``

* ``isNull('') -> false``

* ``isNull(10 + 20) -> false``

* ``isNull(10/0) -> true``

___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Mantıksal OR işleci. Aynı | |

* ``or(true, false) -> true``

* ``true || false -> true``

___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Sayı çiftinin pozitif mod sayısı.

* ``pmod(-20, 8) -> 4``

___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Giriş satırının bulunduğu geçerli bölüm KIMLIĞINI döndürür

* ``partitionId()``

___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Bir sayıyı diğerinin kuvvetine yükseltir

* ``power(10, 2) -> 100``

___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Bir değer grubundaki bir değerin derecesini hesaplar. Sonuç, Bölüm sıralamasına göre geçerli satırdan önceki veya eşit olan satır sayısıdır. Değerler dizide boşluk oluşturur. Sıralama, veriler sıralandığında ve değerlerde değişiklik ararken bile kullanılır

* ``rank(salesQtr, salesAmt)``

___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Öğeleri bir dizide biriktirir. Azaltma, #acc ve #item olarak ilk ifade işlevindeki bir Biriktiricinin ve bir öğenin bir başvurusunu bekliyor ve ikinci ifade işlevinde kullanılacak #result olarak elde edilen değeri bekliyor

* ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10``

* ``reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) -> '01234'``

* ``reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) -> 25``

___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Verili bir Regex deseninin eşleşen alt dizesini ayıklayın. Son parametre, eşleşme grubunu tanımlar ve atlanırsa varsayılan olarak 1 ' e ayarlanır. Kaçış olmadan bir dizeyle eşleştirmek için ' <regex> ' (Back quote) kullanın

* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``

* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Dizenin verilen Regex düzeniyle eşleşip eşleşmediğini denetler. Kaçış olmadan bir dizeyle eşleştirmek için ' <regex> ' (Back quote) kullanın

* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``

* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Bir Regex deseninin tüm yinelemelerini, belirtilen dizedeki başka bir alt dizeyle Değiştir kaçış olmadan bir dizeyle eşleştirmek için ' <regex> ' (arka tırnak) kullanın

* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``

* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Bir dizeyi Regex temelinde bir sınırlayıcı temelinde böler ve dizeler dizisini döndürür

* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``

* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``

* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``

* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``

___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Bir alt dizenin tüm tekrarlamalarını verilen dizedeki başka bir alt dize ile değiştirin. Son parametre atlanırsa, varsayılan olarak boş dize olur

* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``

* ``replace('doggie dog', 'dog', '') -> 'gie '``

* ``replace('doggie dog', 'dog') -> 'gie '``

___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Bir dizeyi tersine çevirir

* ``reverse('gunchus') -> 'suhcnug'``

___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Sağdan karakter sayısıyla bir alt dize ayıklar. Alt DIZEDEN aynı (Str, LENGTH (str)-n, n)

* ``right('bojjus', 2) -> 'us'``

* ``right('bojjus', 20) -> 'bojjus'``

___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Dizenin verilen Regex düzeniyle eşleşip eşleşmediğini denetler

* ``rlike('200.50', `(\d+).(\d+)`) -> true``

* ``rlike('bogus', `M[0-9]+.*`) -> false``

___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Bir sayıyı isteğe bağlı bir ölçek ve isteğe bağlı bir yuvarlama modu olarak yuvarlar. Ölçek atlanırsa, varsayılan olarak 0 olur.  Mod atlanırsa, varsayılan olarak ROUND_HALF_UP (5) olarak ayarlanır. Yuvarlama değerleri şunlardır 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY

* ``round(100.123) -> 100.0``

* ``round(2.5, 0) -> 3.0``

* ``round(5.3999999999999995, 2, 7) -> 5.40``

___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
1 ile başlayan bir penceredeki satırlar için sıralı bir satır numaralandırması atar

* ``rowNumber()``

___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Dizeyi, belirli bir uzunluğa ulaşana kadar sağlanan doldurmaya göre sağ Altlar. Dize uzunluğuna eşit veya daha büyük ise, bu durumda uzunluğa atılır

* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``

* ``rpad('dumbo', 4, '-') -> 'dumb'``

* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``

___
### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Baştaki karakterlerden oluşan dizeyi sağ kırpar. İkinci parametre belirtilmemişse boşluğu kırpar. Aksi takdirde, ikinci parametrede belirtilen karakterleri kırpar

* ``rtrim('  dumbo  ') -> '  dumbo'``

* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``

___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Bir tarihin ikinci değerini alır. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Yerel Saat dilimi varsayılan olarak kullanılır. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``

___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Saniye sayısı için milisaniye cinsinden süre

* ``seconds(2) -> 2000L``

___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Farklı ilkel veri türlerinin sütun kümesinin SHA-1 özetini hesaplar ve 40 karakter onaltılı dize döndürür. Bir satır parmak izini hesaplamak için kullanılabilir

* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``

___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Yalnızca 0 (256), 224, 256, 384, 512 değerlerinin yer aldığı bir bit uzunluğu verilen, farklı ilkel veri türlerinin sütun kümesinin SHA-2 özetini hesaplar. Bir satır parmak izini hesaplamak için kullanılabilir

* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``

___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sinüs değeri hesaplar

* ``sin(2) -> 0.9092974268256817``

___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiperbolik sinüs değerini hesaplar

* ``sinh(0) -> 0.0``

___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun çarpıklığını alır

* ``skewness(sales)``

___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, bir sütunun çarpıklığını alır

* ``skewnessIf(region == 'West', sales)``

___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Bir dizinin alt kümesini bir konumdan ayıklar. Konum 1 tabanlıdır. Uzunluk atlanırsa, varsayılan dize sonuna ayarlanır

* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``

* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``

* ``slice([10, 20, 30, 40], 2)[1] -> 20``

* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``

* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``

* ``slice(['a', 'b', 'c', 'd'], 8) -> []``

___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Belirtilen koşul işlevini kullanarak diziyi sıralar. Sıralama, #item1 ve #item2 olarak ifade işlevindeki birbirini izleyen iki öğeye bir başvuru bekliyor

* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``

* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``

___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Dize için soundex kodunu alır

* ``soundex('genius') -> 'G520'``

___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Bir dizeyi sınırlayıcıya göre böler ve dizeler dizisini döndürür

* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``

* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``

* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``

* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``

* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``

* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``

* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``

___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sayının kare kökünü hesaplar

* ``sqrt(9) -> 3``

___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Dizenin sağlanan dizeyle başlayacağını denetler

* ``startsWith('dumbo', 'du') -> true``

___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun standart sapmasını alır

* ``stdDev(sales)``

___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, bir sütunun standart sapmasını alır

* ``stddevIf(region == 'West', sales)``

___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun popülasyon standart sapmasını alır

* ``stddevPopulation(sales)``

___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, bir sütunun popülasyon standart sapmasını alır

* ``stddevPopulationIf(region == 'West', sales)``

___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun örnek standart sapmasını alır

* ``stddevSample(sales)``

___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, bir sütunun örnek standart sapmasını alır

* ``stddevSampleIf(region == 'West', sales)``

___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Bir tarih veya zaman damgasından ay çıkarın. Tarih için-işleci ile aynı

* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``

___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Tarih veya zaman damgasından ayları çıkar

* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``

___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Belirli bir uzunluktaki alt dizeyi bir konumdan ayıklar. Konum 1 tabanlıdır. Uzunluk atlanırsa, varsayılan dize sonuna ayarlanır

* ``substring('Cat in the hat', 5, 2) -> 'in'``

* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``

* ``substring('Cat in the hat', 5) -> 'in the hat'``

* ``substring('Cat in the hat', 100, 100) -> ''``

___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Sayısal bir sütunun toplam toplamını alır

* ``sum(col)``

___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Sayısal bir sütunun farklı değerlerinin toplam toplamını alır

* ``sumDistinct(col)``

___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ölçütlere dayalı olarak sayısal bir sütunun toplam toplamı alınır. Koşul herhangi bir sütunu temel alabilir

* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``

* ``sumDistinctIf(true, sales)``

___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ölçütlere dayalı olarak sayısal bir sütunun toplam toplamı alınır. Koşul herhangi bir sütunu temel alabilir

* ``sumIf(state == 'CA' && commission < 10000, sales)``

* ``sumIf(true, sales)``

___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir teğet değeri hesaplar

* ``tan(0) -> 0.0``

___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hiperbolik tanjant değerini hesaplar

* ``tanh(0) -> 0.0``

___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Herhangi bir sayısal/Tarih/zaman damgası/dizeyi ikili gösterimine dönüştürür

* ``toBinary(3) -> [0x11]``

___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
('T ', ' true ', ' y ', ' Yes ', ' 1 ') değerini true ve (' f ', ' false ', ' n ', ' No ', ' 0 ') değerini false ve NULL değerine dönüştürür

* ``toBoolean('true') -> true``

* ``toBoolean('n') -> false``

* ``isNull(toBoolean('truthy')) -> true``

___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
İsteğe bağlı bir giriş tarihi biçimi kullanarak giriş tarihi dizesini tarihe dönüştürür. Kullanılabilir biçimler için Java 'nın SimpleDateFormat bölümüne bakın. Giriş tarihi biçimi atlanırsa varsayılan biçim yyyy-[M] M-[d] d biçimindedir. Kabul edilen biçimler şunlardır: [yyyy, yyyy-[M] M, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *]

* ``toDate('2012-8-18') -> toDate('2012-08-18')``

* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``

___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Herhangi bir sayısal veya dizeyi ondalık bir değere dönüştürür. Duyarlık ve ölçek belirtilmemişse, varsayılan olarak ayarlanır (10, 2). Dönüştürme için isteğe bağlı bir Java ondalık biçimi kullanılabilir. En-US, de, zh-CN gibi BCP47 dili biçiminde isteğe bağlı bir yerel ayar biçimi

* ``toDecimal(123.45) -> 123.45``

* ``toDecimal('123.45', 8, 4) -> 123.4500``

* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``

___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Herhangi bir sayısal veya dizeyi bir Double değere dönüştürür. Dönüştürme için isteğe bağlı bir Java ondalık biçimi kullanılabilir. En-US, de, zh-CN gibi BCP47 dili biçiminde isteğe bağlı bir yerel ayar biçimi

* ``toDouble(123.45) -> 123.45``

* ``toDouble('123.45') -> 123.45``

* ``toDouble('$123.45', '$###.00') -> 123.45``

* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``

___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Herhangi bir sayısal veya dizeyi bir float değerine dönüştürür. Dönüştürme için isteğe bağlı bir Java ondalık biçimi kullanılabilir. Herhangi bir Double 'ı keser

* ``toFloat(123.45) -> 123.45f``

* ``toFloat('123.45') -> 123.45f``

* ``toFloat('$123.45', '$###.00') -> 123.45f``

___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Herhangi bir sayısal veya dizeyi bir tamsayı değerine dönüştürür. Dönüştürme için isteğe bağlı bir Java ondalık biçimi kullanılabilir. Long, float, Double ile keser

* ``toInteger(123) -> 123``

* ``toInteger('123') -> 123``

* ``toInteger('$123', '$###') -> 123``

___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Herhangi bir sayısal veya dizeyi uzun bir değere dönüştürür. Dönüştürme için isteğe bağlı bir Java ondalık biçimi kullanılabilir. Tüm float, Double

* ``toLong(123) -> 123``

* ``toLong('123') -> 123``

* ``toLong('$123', '$###') -> 123``

___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Herhangi bir sayısal veya dizeyi bir kısa değere dönüştürür. Dönüştürme için isteğe bağlı bir Java ondalık biçimi kullanılabilir. Tüm tamsayılar, Long, float, Double

* ``toShort(123) -> 123``

* ``toShort('123') -> 123``

* ``toShort('$123', '$###') -> 123``

___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
İlkel bir veri türünü dizeye dönüştürür. Sayı ve tarih için bir biçim belirtilebilir. Belirtilmezse, sistem varsayılanı seçilir. Sayılar için Java Decimal biçimi kullanılır. Olası tüm tarih biçimleri için Java SimpleDateFormat öğesine bakın; Varsayılan biçim yyyy-aa-gg şeklindedir

* ``toString(10) -> '10'``

* ``toString('engineer') -> 'engineer'``

* ``toString(123456.789, '##,###.##') -> '123,456.79'``

* ``toString(123.78, '000000.000') -> '000123.780'``

* ``toString(12345, '##0.#####E0') -> '12.345E3'``

* ``toString(toDate('2018-12-31')) -> '2018-12-31'``

* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``

* ``toString(4 == 20) -> 'false'``

___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Bir dizeyi, isteğe bağlı bir zaman damgası biçimi verilen bir zaman damgasına dönüştürür. Tüm olası biçimler için Java SimpleDateFormat bölümüne bakın. Zaman damgası varsayılan düzende yoksayılırsa. yyyy-[M] M-[d] d hh: mm: SS [. f...] kullanılır. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Zaman damgası, kullanılabilir biçimler için Java 'nın SimpleDateFormat değerini Ifade eden 999'A göre daha fazla milisaniyelik doğruluğu destekler. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``

* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``

* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``

* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``

___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Zaman damgasını UTC 'ye dönüştürür. İsteğe bağlı bir saat dilimini ' GMT ', ' PST ', ' UTC ', ' Amerika/Cayman ' biçiminde geçirebilirsiniz. Kullanılabilir biçimler için geçerli timezoneRefer Java 'nın SimpleDateFormat öğesine varsayılan olarak ayarlanır. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html

* ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``

* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``

___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Bir karakter kümesini dizedeki başka bir karakter kümesiyle değiştirin. Karakterler 1 ile 1 arasında değiştirme

* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``

* ``translate('(gunchus)', '()', '[') -> '[gunchus'``

___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Baştaki ve sondaki karakterlerden oluşan dizeyi kırpar. İkinci parametre belirtilmemişse boşluğu kırpar. Aksi takdirde, ikinci parametrede belirtilen karakterleri kırpar

* ``trim('  dumbo  ') -> 'dumbo'``

* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``

___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Her zaman doğru bir değer döndürür. ' True ' adlı bir sütun varsa işlev sözdizimini (true ()) kullanın

* ``(10 + 20 == 30) -> true``

* ``(10 + 20 == 30) -> true()``

___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Sütunun türüyle eşleşir. Yalnızca desenli ifadelerde kullanılabilir. sayı Short, Integer, Long, Double, float veya Decimal ile eşleşir, tamsayısı Short, Integer, Long, kesirli eşleşir Double, float, Decimal ve DateTime ile tarih veya zaman damgası türüyle eşleşir

* ``typeMatch(type, 'number')``

* ``typeMatch('date', 'datetime')``

___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Büyük/büyük durumlar dize

* ``upper('bojjus') -> 'BOJJUS'``

___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Oluşturulan UUID 'yi döndürür

* ``uuid()``

___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun varyansını alır

* ``variance(sales)``

___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, bir sütunun farkını alır

* ``varianceIf(region == 'West', sales)``

___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun popülasyon varyansını alır

* ``variancePopulation(sales)``

___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere göre, bir sütunun popülasyon varyansını alır

* ``variancePopulationIf(region == 'West', sales)``

___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Bir sütunun taraflı olmayan varyansını alır

* ``varianceSample(sales)``

___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ölçütlere dayalı olarak, bir sütunun taraflı olmayan varyansını alır

* ``varianceSampleIf(region == 'West', sales)``

___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Bir tarih verilen yılın haftasını alır

* ``weekOfYear(toDate('2008-02-20')) -> 8``

___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Hafta sayısı için milisaniye cinsinden süre

* ``weeks(2) -> 1209600000L``

___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Mantıksal XOR işleci. ^ İşleci ile aynı

* ``xor(true, false) -> true``

* ``xor(true, true) -> false``

* ``true ^ false -> true``

___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Tarihin yıl değerini alır

* ``year(toDate('2012-8-8')) -> 2012``


## <a name="next-steps"></a>Sonraki adımlar

[Ifade oluşturucuyu nasıl kullanacağınızı öğrenin](concepts-data-flow-expression-builder.md).
