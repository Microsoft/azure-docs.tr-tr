---
title: Date_Bucket (Transact-SQL)-Azure SQL Edge
description: Azure SQL Edge 'de Date_Bucket kullanma hakkında bilgi edinin
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 9d81419721e94a2e181f094c0e0e64b1b23544a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073528"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Bu işlev, her bir tarih saat demetinin başına, parametresi tarafından tanımlanan zaman damgasından `origin` veya origin parametresi belirtilmemişse varsayılan Origin değeri ile eşleşen tarih saat aralığını döndürür `1900-01-01 00:00:00.000` . 

Tüm Transact-SQL tarih ve saat veri türleri ve işlevlerine genel bir bakış için bkz. [Tarih ve saat veri türleri ve işlevleri &#40;Transact-sql&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) .

[Transact-SQL sözdizimi kuralları](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>Söz dizimi

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>Bağımsız değişkenler

*Işlevindeki*

' Number ' parametresiyle kullanılan *tarihin* bölümü. Örn. Yıl, ay, dakika, saniye vb.

> [!NOTE]
> `DATE_BUCKET`*Dateppart* bağımsız değişkenleri için Kullanıcı tanımlı değişken eşdeğerlerini kabul etmez.
  
|*Işlevindeki*|Kısaltmaları|  
|---|---|
|**günündeki**|**dd**, **d**|  
|**hafta**|**hafta**, **WW**| 
|**ay**|**AA**, **dk**|
|**dönem**|**QQ**, **q**|  
|**yıl**|**yy**, **yyyy**|  
|**saate**|**hh**|  
|**dakikaya**|**mı**, **n**|  
|**İkincisi**|**SS**, **s**|  
|**milisaniy**|**SWM**|  

*sayısından*

Değer, *datePart* bağımsız değişkeniyle birleştirilmiş olan demet genişliğine karar veren tamsayı numarası. Bu, kaynak zamandan itibaren veri bölümü demetlerinin genişliğini temsil eder. **`This argument cannot be a negative integer value`**. 

*date*

Aşağıdaki değerlerden birine çözümleyebilecek bir ifade:

+ **date**
+ **datetime**
+ **türünde**
+ **datetime2**
+ **girişin**
+ **ışınızda**

*Tarih* için, `DATE_BUCKET` yukarıda belirtilen veri türlerine çözümlendiklerinde bir sütun ifadesi, ifade veya Kullanıcı tanımlı değişken kabul eder.

**Tıdır** 

Aşağıdaki değerlerden birine çözümleyebilecek isteğe bağlı bir ifade:

+ **date**
+ **datetime**
+ **türünde**
+ **datetime2**
+ **girişin**
+ **ışınızda**

İçin veri türü, `Origin` parametrenin veri türüyle eşleşmelidir `Date` . 

`DATE_BUCKET` işlev için bir kaynak değer belirtilmemişse, Ocak 12:00 ' de, varsayılan bir başlangıç tarihi değeri olan `1900-01-01 00:00:00.000` örn. Pazartesi, 1 1900 Ocak.

## <a name="return-type"></a>Dönüş Türü

Bu yöntemin dönüş değeri veri türü dinamiktir. Dönüş türü, için sağlanan bağımsız değişkene bağlıdır `date` . İçin geçerli bir giriş veri türü sağlanırsa `date` , `DATE_BUCKET` aynı veri türünü döndürür. `DATE_BUCKET` parametresi için bir dize sabit değeri belirtilmişse bir hata oluşturur `date` .

## <a name="return-values"></a>Dönüş Değerleri

### <a name="understanding-the-output-from-date_bucket"></a>Çıktıyı anlama `DATE_BUCKET`

`Date_Bucket` datePart ve Number parametresine karşılık gelen en geç tarih veya saat değerini döndürür. Örneğin, aşağıdaki ifadelerde çıkış `Date_Bucket` değeri döndürülür, `2020-04-13 00:00:00.0000000` çünkü çıkış varsayılan kaynak zamanından itibaren bir hafta demetlerine göre hesaplanır `1900-01-01 00:00:00.000` . Değer, `2020-04-13 00:00:00.0000000` kaynak değerinden 6276 haftadan oluşur `1900-01-01 00:00:00.000` . 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Aşağıdaki tüm ifadeler için aynı çıkış değeri `2020-04-13 00:00:00.0000000` döndürülecek. Bunun nedeni, `2020-04-13 00:00:00.0000000` kaynak tarihinden itibaren 6276 hafta ve 6276, 2, 3, 4 ve 6 ' a bölünebilmelidir.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

Aşağıdaki ifade için çıkış, `2020-04-06 00:00:00.0000000` varsayılan kaynak saatten 6275 hafta olan `1900-01-01 00:00:00.000` .

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

Aşağıdaki ifade için çıkış, `2020-06-09 00:00:00.0000000` belirtilen kaynak saatten 75 hafta olan `2019-01-01 00:00:00` .

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>datepart bağımsız değişkeni

**DayOfYear**, **Day** ve **HAFTANINGÜNÜ** aynı değeri döndürür. Her *datepart* ve kısaltmalar aynı değeri döndürür.
  
## <a name="number-argument"></a>sayı bağımsız değişkeni

*Sayı* bağımsız değişkeni pozitif **int** değerlerinin aralığını aşamaz. Aşağıdaki deyimlerde, *sayı* bağımsız değişkeni **int** ile 1 aralığını aşıyor. Aşağıdaki ifade şu hata iletisini döndürür: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

İşleve negatif bir sayı değeri geçirilirse `Date_Bucket` , aşağıdaki hata döndürülür. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>Tarih bağımsız değişkeni  

`DATE_BUCKET` bağımsız değişkenin veri türüne karşılık gelen temel değeri döndürün `date` . Aşağıdaki örnekte, datetime2 veri türüne sahip bir çıkış değeri döndürülür. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>kaynak bağımsız değişkeni  

`origin`İçindeki ve bağımsız değişkenlerin veri türü `date` aynı olmalıdır. Farklı veri türleri kullanılıyorsa bir hata oluşturulur.

## <a name="remarks"></a>Açıklamalar

`DATE_BUCKET`Aşağıdaki yan tümcelerde kullanın:

+ GROUP BY
+ HAVING
+ SİPARİŞ VEREN
+ SEÇIN \<list>
+ WHERE

## <a name="examples"></a>Örnekler

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. Kaynak zamanından 1 demet genişliğine sahip Date_Bucket hesaplanıyor

Bu deyimlerden her biri, kaynak zamandan 1 demet genişliğiyle *date_bucket* artırır:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Sonuç kümesini burada bulabilirsiniz.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. İfadeleri sayı ve Tarih parametreleri için bağımsız değişken olarak kullanma

Bu örnekler, *sayı* ve *Tarih* parametreleri için bağımsız değişken olarak farklı tür ifadeler kullanır. Bu örnekler ' AdventureWorksDW2017 ' veritabanı kullanılarak oluşturulmuştur.
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Kullanıcı tanımlı değişkenleri sayı ve tarih olarak belirtme  

Bu örnek, *sayı* ve *Tarih* için bağımsız değişken olarak Kullanıcı tanımlı değişkenleri belirtir:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Sonuç kümesini burada bulabilirsiniz.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Tarih olarak bir sütun belirtme

Aşağıdaki örnekte, haftalık Tarih demetlerine göre gruplanan OrderQuantity ve BirimFiyat toplamının toplamını hesapladık.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Sonuç kümesini burada bulabilirsiniz.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Skalar sistem işlevini tarih olarak belirtme

Bu örnek, `SYSDATETIME` *tarihi* belirtir. Döndürülen tam değer deyimin çalışmasının günü ve saatine bağlıdır:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Sonuç kümesini burada bulabilirsiniz.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Skaler alt sorgular ve skaler işlevleri sayı ve tarih olarak belirtme

Bu örnek, `MAX(OrderDate)` *sayı* ve *Tarih* için bağımsız değişkenler olarak skaler alt sorgular kullanır. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` değer listesinden bir *sayı* bağımsız değişkeninin nasıl ekleneceğini göstermek için sayı parametresi için yapay bir bağımsız değişken işlevi görür.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Sayısal ifadeler ve skaler sistem işlevlerini sayı ve tarih olarak belirtme

Bu örnek, sayı ve tarih için bağımsız değişkenler olarak sayısal bir ifade ((10/2)) ve skaler sistem işlevleri (SYSDATETIME) kullanır.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Toplam pencere işlevini sayı olarak belirtme

Bu örnek, *sayı* için bir bağımsız değişken olarak bir toplam pencere işlevi kullanır.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. Varsayılan olmayan bir kaynak değeri kullanma

Bu örnek, tarih demetlerini oluşturmak için varsayılan olmayan bir orgın değeri kullanır. 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>Ayrıca bkz.

[&#40;Transact-SQL&#41;atama ve dönüştürme ](/sql/t-sql/functions/cast-and-convert-transact-sql/)
