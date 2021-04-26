---
title: Sorgu hızlandırma SQL dil başvurusu
titleSuffix: Azure Storage
description: Sorgu hızlandırma SQL söz dizimini nasıl kullanacağınızı öğrenin.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: ca4fe1410c18357a1fab10cc9c971cf3a81542fd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963266"
---
# <a name="query-acceleration-sql-language-reference"></a>Sorgu hızlandırma SQL dil başvurusu

Sorgu hızlandırma, blob içerikleri üzerinde sorguları ifade etmek için ANSI SQL benzeri bir dili destekler.  Sorgu hızlandırma SQL diyalekti, desteklenen veri türleri, işleçler vb. sınırlı bir kümesi olan ANSI SQL 'nin bir alt kümesidir, ancak JSON gibi hiyerarşik yarı yapılandırılmış veri biçimleri üzerinde sorguları desteklemek için ANSI SQL 'de de genişletilir. 

## <a name="select-syntax"></a>Sözdizimi Seç

Sorgu hızlandırma tarafından desteklenen tek SQL deyimleri SELECT deyimidir. Bu örnek, ifadenin true döndüğü her satırı döndürür.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

CSV biçimli veriler için *tablo* olmalıdır `BlobStorage` .  Bu, sorgunun REST çağrısında belirtilen Blobun göre çalışacağı anlamına gelir.
JSON biçimli veriler için *tablo* bir "Tablo tanımlayıcısıdır."   Bu makalenin [tablo tanımlayıcıları](#table-descriptors) bölümüne bakın.

Aşağıdaki örnekte, WHERE *ifadesinin* true döndüğü her satır için, bu deyim, projeksiyon ifadelerinin her birini değerlendirmeden oluşturulan yeni bir satır döndürür.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Aşağıdaki örnek bir toplam hesaplama (örneğin, belirli bir sütunun ortalama değeri), *ifadenin* true döndüğü her bir satır için döndürür. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

Aşağıdaki örnek, CSV biçimli bir blobu bölmek için uygun uzaklıkları döndürür.  Bu makalenin [sys. Split](#sys-split) bölümüne bakın.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Veri Türleri

|Veri Türü|Açıklama|
|---------|-------------------------------------------|
|INT      |64-bit işaretli tamsayı.                     |
|FLOAT    |64-bit ("çift duyarlıklı") kayan nokta.|
|DIZISINDE   |Değişken uzunlukta Unicode dizesi.            |
|TIMESTAMP|Zaman içinde bir nokta.                           |
|BOOLEAN  |True veya false.                             |

CSV biçimli verilerden değerleri okurken tüm değerler dizeler olarak okunurdur.  Dize değerleri, atama ifadeleri kullanılarak diğer türlere dönüştürülebilir.  Değerler, bağlama göre örtük olarak diğer türlere dönüştürülebilir. daha fazla bilgi için bkz. [veri türü önceliği (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

## <a name="expressions"></a>İfadeler

### <a name="referencing-fields"></a>Başvurulan alanlar

JSON biçimli veriler veya üst bilgi satırı içeren CSV biçimli veriler için alanlara ad tarafından başvurulabilir.  Alan adları tırnak içine alınmış veya tırnak içine alınmış olabilir. Tırnak içine alan adları çift tırnak (") içinde, boşluk içerebilir ve büyük/küçük harfe duyarlıdır.  Tırnak işaretleri olmayan alan adları büyük/küçük harfe duyarlıdır ve özel karakter içeremez.

CSV biçimli verilerde alanlara bir alt çizgi (_) karakteriyle önek olarak de başvurulabilir.  Örneğin, ilk alana _1 olarak başvurulabilir veya on birinci alanına _11 olarak başvurulabilir.  Alanlara göre başvuruda bulunmak, üst bilgi satırı içermeyen CSV biçimli veriler için yararlıdır, bu durumda belirli bir alana başvurmak için tek yol sıralı olur.

### <a name="operators"></a>İşleçler

Aşağıdaki standart SQL işleçleri desteklenir:

|İşleç|Açıklama|
|--|--|
|[=](/sql/t-sql/language-elements/equals-transact-sql)    |İki ifadenin eşitliğini karşılaştırır (bir karşılaştırma işleci).|
|[!=](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |Bir ifadenin başka bir ifadeye (karşılaştırma operatörü) eşit olup olmadığını sınar.|
|[<>](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |Eşit değildir (bir karşılaştırma operatörü) için iki ifadeyi karşılaştırır.|
|[<](/sql/t-sql/language-elements/less-than-transact-sql)    |Küçüktür (bir karşılaştırma operatörü) için iki ifadeyi karşılaştırır.|
|[<=](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |Küçüktür veya eşittir (bir karşılaştırma operatörü) için iki ifadeyi karşılaştırır.|
|[>](/sql/t-sql/language-elements/greater-than-transact-sql)    |Büyüktür (bir karşılaştırma operatörü) için iki ifadeyi karşılaştırır. |
|[>=](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |İki ifadeyi daha büyük veya eşit (bir karşılaştırma operatörü) için karşılaştırır.|
|[+](/sql/t-sql/language-elements/add-transact-sql)    |İki sayı ekler. Bu ek aritmetik işleç Ayrıca bir tarihe kadar gün cinsinden bir sayı ekleyebilir.|
|[-](/sql/t-sql/language-elements/subtract-transact-sql)    |İki sayıyı çıkartır (aritmetik çıkarma işleci). |
|[/](/sql/t-sql/language-elements/divide-transact-sql)    |Bir sayıyı başka bir sayıya böler (aritmetik bölme işleci).|
|[*](/sql/t-sql/language-elements/multiply-transact-sql)    |İki ifadeyi çarpar (aritmetik çarpma işleci).|
|[%](/sql/t-sql/language-elements/modulo-transact-sql)    |Bir sayının başka bir sayıya bölünen kalanını döndürür.|
|[AND](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |İki tamsayı değeri arasında bit düzeyinde mantıksal ve işlem gerçekleştirir.|
|[OR](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Transact-SQL deyimlerindeki ikili ifadelere çevrilmiş olarak belirtilen iki tamsayı değeri arasında bit düzeyinde mantıksal veya işlem gerçekleştirir.|
|[NOT](/sql/t-sql/language-elements/not-transact-sql)    |Boole girişi geçersiz kılar.|
|[CAST](/sql/t-sql/functions/cast-and-convert-transact-sql)    |Bir veri türündeki bir ifadeyi bir diğerine dönüştürür.|
|[ARALARıNDA](/sql/t-sql/language-elements/between-transact-sql)    |Sınanacak aralığı belirtir.|
|['NDAKI](/sql/t-sql/language-elements/in-transact-sql)    |Belirtilen değerin alt sorgu veya listedeki herhangi bir değerle eşleşip eşleşmediğini belirler.|
|[NULLıF](/sql/t-sql/language-elements/nullif-transact-sql)    |Belirtilen iki ifade eşitse null değeri döndürür.|
|[COALESCE](/sql/t-sql/language-elements/coalesce-transact-sql)    |Bağımsız değişkenleri sırayla değerlendirir ve başlangıçta NULL olarak değerlendirilmeyen ilk ifadenin geçerli değerini döndürür.|

Bir işlecin sol ve sağ tarafındaki veri türleri farklıysa, otomatik dönüştürme burada belirtilen kurallara göre gerçekleştirilir: [veri türü önceliği (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

Sorgu hızlandırma SQL dili, bu makalede ele alınan veri türlerinin yalnızca çok küçük bir alt kümesini destekler.  Bu makalenin [veri türleri](#data-types) bölümüne bakın.

### <a name="casts"></a>Podcast

Sorgu hızlandırma SQL dili, şu kurallara göre atama işlecini destekler: [veri türü dönüştürme (veritabanı altyapısı)](/sql/t-sql/data-types/data-type-conversion-database-engine).  

Sorgu hızlandırma SQL dili, bu makalede ele alınan veri türlerinin yalnızca küçük bir alt kümesini destekler.  Bu makalenin [veri türleri](#data-types) bölümüne bakın.

### <a name="string-functions"></a>Dize işlevleri

Sorgu hızlandırma SQL dili, aşağıdaki standart SQL dize işlevlerini destekler:

|İşlev|Açıklama|
|--|--|
|CHAR_LENGTH    | Dize ifadesinin karakter veri türünde olması halinde, dize ifadesinin karakter cinsinden uzunluğunu döndürür; Aksi takdirde, dize ifadesinin bayt cinsinden uzunluğunu döndürür (en küçük tamsayı, 8 ' den bölünen bit sayısından küçük değildir). (Bu işlev CHARACTER_LENGTH işleviyle aynıdır.)|
|CHARACTER_LENGTH    |Dize ifadesinin karakter veri türünde olması halinde, dize ifadesinin karakter cinsinden uzunluğunu döndürür; Aksi takdirde, dize ifadesinin bayt cinsinden uzunluğunu döndürür (en küçük tamsayı, 8 ' den bölünen bit sayısından küçük değildir). (Bu işlev CHAR_LENGTH işleviyle aynıdır|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |Büyük harfli karakter verilerini küçük harfe dönüştürdükten sonra bir karakter ifadesi döndürür.|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |Küçük harfli karakter verisi büyük harfe dönüştürülmüş bir karakter ifadesi döndürür.|
|[DIZEDEN](/sql/t-sql/functions/substring-transact-sql)    |SQL Server bir karakter, ikili, metin veya resim ifadesinin bir parçasını döndürür.|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |Bir dizenin başından ve sonundan sonra char karakter (32) veya diğer belirtilen karakterleri kaldırır.|
|LIDERI    |Bir dizenin başından karakter (32) veya belirtilen diğer karakterleri kaldırır.|
|ARKASıNDA    |Bir dizenin sonundaki karakter karakteri (32) veya belirtilen diğer karakterleri kaldırır.|

İşte birkaç örnek:

|İşlev|Örnek|Sonuç|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>Tarih işlevleri

Aşağıdaki standart SQL tarih işlevleri desteklenir:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Şu anda [Standart IS08601 'in tüm tarih biçimlerini](https://www.w3.org/TR/NOTE-datetime)dönüştürüyoruz. 

#### <a name="date_add-function"></a>DATE_ADD işlevi

Sorgu hızlandırma SQL dili, işlevin yıl, ay, gün, saat, dakika ve saniyeyi destekler ``DATE_ADD`` .

Örnekler:

' ' SQL DATE_ADD (datepart, Quantity, timestamp) DATE_ADD (' dakika ', 1, CAST (' 2017-01-02T03:04:05.006 Z ' zaman DAMGASı olarak)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT işlevi

İşlev için desteklenen tarih bölümü dışında ayıklama için ``DATE_ADD`` , sorgu HıZLANDıRMA SQL dili timezone_hour ve timezone_minute Tarih parçası olarak destekler.

Örnekler:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING işlevi

Örnekler:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

Bu tablo, işlevinin çıkış biçimini belirtmek için kullanabileceğiniz dizeleri açıklar ``TO_STRING`` .

|Biçimlendirme dizesi    |Çıktı                               |
|-----------------|-------------------------------------|
|yy               |Yıl 2 basamak biçiminde – 1999 ' 99 ' olarak|
|y                |4 basamaklı biçimde yıl               |
|yyyy             |4 basamaklı biçimde yıl               |
|M                |Yılın ayı – 1                    |
|MM               |Sıfır doldurulmuş ay – 01               |
|AAA              |İşlenir. Yılın ayı-JAN            |
|MMMM             |Tam ay – Mayıs                      |
|d                |Ayın günü (1-31)                  |
|Ekle               |Ayın sıfır doldurulmuş günü (01-31)     |
|a                |HAR veya PM                             |
|h                |Günün saati (1-12)                   |
|hh               |Sıfır saatlik saat od günü (01-12)     |
|H                |Günün saati (0-23)                   |
|HH               |Gün sıfır saat saat (00-23)      |
|m                |Saatin dakikası (0-59)                |
|mm               |Sıfır doldurulmuş dakika (00-59)           |
|s                |Dakikaların saniyesi (0-59)             |
|ss               |Sıfır doldurulmuş saniye (00-59)          |
|S                |Saniye kesri (0,1-0.9)        |
|SS               |Saniye kesri (0,01-0,99)      |
|SSS              |Saniye kesri (0,001-0.999)    |
|X                |Saat cinsinden Aralık                      |
|XX veya XXXX       |Saat ve dakika cinsinden fark (+ 0430)  |
|XXX veya XXXXX     |Saat ve dakika cinsinden fark (-07:00) |
|x                |Saat cinsinden fark (7)                  |
|xx veya xxxx       |Saat ve dakika cinsinden fark (+ 0530)    |
|Xxx veya xxxxx     |Saat ve dakika cinsinden fark (+ 05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP işlevi

Yalnızca IS08601 biçimleri desteklenir.

Örnekler:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Ayrıca, ``UTCNOW`` Sistem saatini almak için işlevini de kullanabilirsiniz.


## <a name="aggregate-expressions"></a>Toplama Ifadeleri

SELECT deyimi bir veya daha fazla İzdüşüm ifadesi ya da tek bir toplama ifadesi içerebilir.  Aşağıdaki toplama ifadeleri desteklenir:

|İfade|Açıklama|
|--|--|
|[COUNT ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Koşul ifadesiyle eşleşen kayıt sayısını döndürür.|
|[COUNT (ifade)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |İfadenin null olmayan kayıt sayısını döndürür.|
|[AVERAGE (ifade)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |İfadenin null olmayan değerlerinin ortalamasını döndürür.|
|[MIN (ifade)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |İfadenin null olmayan en küçük değerini döndürür.|
|[MAX (ifade](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |İfadenin null olmayan en büyük değerini döndürür.|
|[SUM (ifade)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |İfadenin null olmayan tüm değerlerinin toplamını döndürür.|

### <a name="missing"></a>BULUNMAYAN

``IS MISSING``İşleç, sorgu HıZLANDıRMA SQL dilinin desteklediği tek standart olmayan bir dildir.  JSON verileri için belirli bir giriş kaydındaki bir alan eksikse, ifade alanı ``IS MISSING`` true Boole değeri olarak değerlendirilir.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Tablo tanımlayıcıları

CSV verileri için tablo adı her zaman olur `BlobStorage` .  Örnek:

```sql
SELECT * FROM BlobStorage
```

JSON verileri için ek seçenekler mevcuttur:

```sql
SELECT * FROM BlobStorage[*].path
```

Bu, JSON verilerinin alt kümeleri üzerinde sorgulara izin verir.

JSON sorguları için FROM yan tümcesinin bir parçası olan yoldan bahsetmeniz gerekir. Bu yollar JSON verilerinin alt kümesini ayrıştırmaya yardımcı olur. Bu yollar, JSON dizisine ve nesne değerlerine başvurabilir.

Bunu daha ayrıntılı olarak anlamak için bir örnek alalım.

Bu örnek verilerimize örnektir:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Yalnızca `warehouses` Yukarıdaki verilerden JSON nesnesiyle ilgileniyor olabilirsiniz. `warehouses`Nesnesi BIR JSON dizisi türüdür, bu nedenle bunu from yan tümcesinde bahsetmeniz gerekir. Örnek sorgunuz aşağıdakine benzer şekilde görünür.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Sorgu tüm alanları alır ancak yalnızca Enlem ' i seçer.

Yalnızca `dimensions` JSON nesne değerine erişmek isterseniz, sorgunuzda bu nesneye başvurabilirsiniz ' i kullanabilirsiniz. Örnek:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Bu ayrıca nesnenin üyelerine erişiminizi sınırlandırır `dimensions` . JSON alanlarının diğer üyelerine ve JSON nesnelerinin iç değerlerine erişmek istiyorsanız, aşağıdaki örnekte gösterildiği gibi bir sorgu kullanabilirsiniz:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage ve BlobStorage [ \* ] her ikisi de tüm nesneye başvurur. Bununla birlikte, FROM yan tümcesinde bir yolunuz varsa BlobStorage [ \* ]. Path kullanmanız gerekir

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. Split

Bu, yalnızca CSV biçimli veriler için kullanılabilen, SELECT ifadesinin özel bir biçimidir.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

CSV veri kayıtlarını toplu olarak indirmek ve işlemek istediğiniz durumlarda bu ifadeyi kullanın. Bu şekilde, kayıtları tek seferde indirmek yerine, paralel olarak işleyebilirsiniz. Bu ifade CSV dosyasından kayıtları döndürmez. Bunun yerine, toplu iş boyutlarının bir koleksiyonunu döndürür. Daha sonra her toplu iş boyutunu kullanarak veri kayıtlarını toplu olarak alabilirsiniz. 

Her toplu işin içermesini istediğiniz bayt sayısını belirtmek için *split_size* parametresini kullanın. Örneğin, aynı anda yalnızca 10 MB 'lık verileri işlemek istiyorsanız, şu şekilde ifade edersiniz: `SELECT sys.split(10485760)FROM BlobStorage` 10 MB, 10.485.760 bayta eşit. Her Batch, bu 10 MB 'a sığacak kadar çok kayıt içerecektir. 

Çoğu durumda, her bir toplu işin boyutu belirttiğiniz sayıdan biraz daha yüksek olacaktır. Bunun nedeni, bir Batch 'in kısmi bir kayıt içeremeyeceği. Bir toplu işteki son kayıt eşiğin sonundan önce başlarsa, toplu işlem, tüm kaydı içerebilecek şekilde daha büyük olur. Son toplu işlemin boyutu büyük olasılıkla belirttiğiniz boyuttan daha küçük olacaktır.

>[!NOTE]
> Split_size en az 10 MB (10485760) olmalıdır.

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Data Lake Storage sorgu hızlandırma](data-lake-storage-query-acceleration.md)
- [Azure Data Lake Storage sorgu hızlandırmasını kullanarak verileri filtreleme](data-lake-storage-query-acceleration-how-to.md)