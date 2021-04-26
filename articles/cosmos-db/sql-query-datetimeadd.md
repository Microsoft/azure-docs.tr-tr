---
title: DateTimeAdd Azure Cosmos DB sorgu dili
description: Azure Cosmos DB SQL sistem işlevi DateTimeAdd ekleme hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c04c63a5ec72f08807b1702f74db39e00662656f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597666"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Belirtilen bir değer değerini (işaretli tamsayı olarak) belirtilen bir DateTime dizesine eklemekten kaynaklanan DateTime dize değerini döndürür  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Bağımsız değişkenler
  
*DateTimePart*  
   DateTimeAdd 'in bir tamsayı numarası eklediği tarihin parçası. Bu tablo tüm geçerli DateTimePart bağımsız değişkenlerini listeler:

| DateTimePart | kısaltmaları        |
| ------------ | -------------------- |
| Yıl         | "Year", "yyyy", "yy" |
| Ay        | "ay", "AA", "m"   |
| Gün          | "gün", "gg", "d"     |
| Saat         | "saat", "hh"         |
| Dakika       | "Minute", "mı", "n"  |
| Second       | "saniye", "ss", "s"  |
| Milisaniy  | "milisaniyelik", "MS"  |
| Mikrosaniye ölçeğinde  | "mikro saniye", "MCS" |
| Nanosaniyelik   | "nanosaniyelik", "NS"   |

*numeric_expr*  
   Belirtilen DateTime 'ın DateTimePart öğesine eklenecek işaretli bir tamsayı değeridir

*Tarih Saat*  
   Şu biçimdeki UTC Tarih ve saat ISO 8601 dize değeri `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
|Biçimlendir|Açıklama|
|-|-|
|YYYY|dört basamaklı yıl|
|MM|iki basamaklı ay (01 = Ocak, vb.)|
|DD|iki basamaklı ayın günü (01 ile 31 arasında)|
|T|zaman öğelerinin başlangıcı için signifier|
|hh|iki basamaklı saat (00 ile 23 arasında)|
|mm|iki basamaklı dakika (00 ila 59)|
|ss|iki basamaklı saniyeler (00 ila 59)|
|. fffffff|yedi basamaklı kesirli saniye|
|Z|UTC (Eşgüdümlü Evrensel Saat) göstergesi|
  
  ISO 8601 biçimi hakkında daha fazla bilgi için bkz. [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>Dönüş türleri

Şu biçimde bir UTC Tarih ve saat ISO 8601 dize değeri döndürür `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
|Biçimlendir|Açıklama|
|-|-|
|YYYY|dört basamaklı yıl|
|MM|iki basamaklı ay (01 = Ocak, vb.)|
|DD|iki basamaklı ayın günü (01 ile 31 arasında)|
|T|zaman öğelerinin başlangıcı için signifier|
|hh|iki basamaklı saat (00 ile 23 arasında)|
|mm|iki basamaklı dakika (00 ila 59)|
|ss|iki basamaklı saniyeler (00 ila 59)|
|. fffffff|yedi basamaklı kesirli saniye|
|Z|UTC (Eşgüdümlü Evrensel Saat) göstergesi|

## <a name="remarks"></a>Açıklamalar

DateTimeAdd `undefined` aşağıdaki nedenlerle döndürülür:

- Belirtilen DateTimePart değeri geçersiz
- Belirtilen numeric_expr geçerli bir tamsayı değil
- Bağımsız değişkende veya sonuçdaki tarih saat geçerli bir ISO 8601 tarih saati değil.

## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, DateTime öğesine 1 ay ekler: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

Aşağıdaki örnek, tarih saatten 2 saat çıkartır: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
