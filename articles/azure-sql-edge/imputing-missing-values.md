---
title: Zaman boşlukları dolduruluyor ve eksik değerleri ayarlama-Azure SQL Edge
description: Azure SQL Edge 'de saat boşluklarını doldurma ve eksik değerleri ayarlama hakkında bilgi edinin
keywords: SQL Edge, zaman serisi
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185598"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Zaman boşlukları dolduruluyor ve eksik değerleri ayarlama 

Zaman serisi verileriyle ilgilenirken, genellikle zaman serisi verilerinde özniteliklerin değerleri eksik olabilir. Verilerin doğası veya veri koleksiyonundaki kesintiler nedeniyle, veri kümesinde zaman *aralıkları* olduğu için de bu da olasıdır.

Örneğin, bir akıllı cihaz için enerji kullanım istatistikleri toplanırken, cihaz işlemsel olmadığında Kullanım istatistiklerinde boşluklar olur. Benzer şekilde, bir makine telemetri verileri toplama senaryosunda, farklı algılamaların verileri farklı sıklıklara yaymak üzere yapılandırılması olasıdır ve bu da sensörler için eksik değerler oluşmasına neden olur. Örneğin, sırasıyla 100 Hz ve 10 Hz sıklığında yapılandırılan iki sensör, voltaj ve basınç varsa, voltaj algılayıcısı verileri her bir yüz bir saniyede yayar, basınç algılayıcısı ise yalnızca her bir saniyede bir veri yayacaktır.

Aşağıdaki tabloda, tek saniyelik bir aralıkta toplanan bir makine telemetri veri kümesi açıklanmaktadır. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Önceki veri kümesinin iki önemli özelliği vardır. 

- Veri kümesi, bazı zaman damgalarına,,, `2020-09-07 06:14:47.000` `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` ve ile ilgili herhangi bir veri noktası içermez `2020-09-07 06:14:55.000` . Bu zaman damgaları *veri kümesindeki boşluklardır.*  
- Voltaj ve basınç okumaları için olarak temsil edilen eksik değerler vardır `null` . 

## <a name="gap-filling"></a>Boşluk doldurma 

Boşluk doldurma, zaman serisi verilerinin analizini kolaylaştırmak için ardışık ve sıralı zaman damgası kümesi oluşturulmasına yardımcı olan bir tekniktir. Azure SQL Edge 'de, zaman serisi veri kümesinde boşlukları doldurmanın en kolay yolu, istenen saat dağıtımına sahip geçici bir tablo tanımlamak ve sonra `LEFT OUTER JOIN` `RIGHT OUTER JOIN` veri kümesi tablosunda bir veya bir işlem yapmanız. 

`MachineTelemetry`Yukarıda temsil edilen verileri örnek olarak almak için aşağıdaki sorgu, analiz için ardışık ve sıralı zaman damgası kümesi oluşturmak için kullanılabilir. 

> [!NOTE]
> Aşağıdaki sorgu eksik satırları, özniteliklerin zaman damgası değerlerini ve `null` değerlerini oluşturur. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
Yukarıdaki sorgu belirtilen aralıktaki tüm *ikinci bir* zaman damgasını içeren aşağıdaki çıktıyı üretir.

Sonuç kümesi şöyledir

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Eksik değerleri verme

Yukarıdaki sorgu, veri analizi için eksik zaman damgalarını oluşturdu, ancak eksik değerlerin (null olarak gösterilen) hiçbirini `voltage` ve `pressure` ayarlarını değiştirmez. Azure SQL Edge 'de, T-SQL ve işlevlerine yeni bir sözdizimi eklenmiştir `LAST_VALUE()` ve `FIRST_VALUE()` Bu, veri kümesindeki önceki veya sonraki değerlere göre, eksik değerlere yönelik mekanizmalar sağlar. 

Yeni sözdizimi `IGNORE NULLS` `RESPECT NULLS` ve işlevleri için ve yan tümcesini `LAST_VALUE()` ekler `FIRST_VALUE()` . Veri kümesindeki aşağıdaki sorgu eksik değerleri, `MachineTelemetry` veri kümesindeki son gözlenen değerle değiştirdiğiniz last_value işlevini kullanarak hesaplar.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Sonuç kümesi şöyledir

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

Aşağıdaki sorgu, hem hem de işlevini kullanarak eksik değerleri bir şekilde yükseltir `LAST_VALUE()` `FIRST_VALUE` . İçin, `ImputedVoltage` eksik değerler çıkış sütunu son gözlenen değerle değiştirilmiştir, çıkış sütunu için `ImputedPressure` eksik değerler veri kümesindeki bir sonraki gözlenen değerle değiştirilmiştir. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Sonuç kümesi şöyledir

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> Yukarıdaki sorgu `FIRST_VALUE()` eksik değerleri sonraki gözlenen değerle değiştirmek için işlevini kullanır. Aynı sonuç `LAST_VALUE()` işlev yan tümcesiyle birlikte kullanılarak elde edilebilir `ORDER BY <ordering_column> DESC` .

## <a name="next-steps"></a>Sonraki adımlar 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Toplama Işlevleri (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)