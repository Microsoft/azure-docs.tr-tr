---
title: Azure Cosmos DB sorgu dilinde Datetımediff
description: Azure Cosmos DB 'de SQL sistem işlevi Datetımediff hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595658"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Belirtilen *StartDate* ve *EndDate* arasında çapraz belirtilen datetimepart sınırlarının sayısını (işaretli tamsayı değeri olarak) döndürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*Başlangıç*  
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

*EndDate*  
   Biçimde UTC Tarih ve saat ISO 8601 dize değeri `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Dönüş türleri

İşaretli bir tamsayı değeri döndürür.

## <a name="remarks"></a>Açıklamalar

Datetımediff `undefined` aşağıdaki nedenlerle döndürülür:

- Belirtilen DateTimePart değeri geçersiz
- StartDate veya EndDate geçerli bir ISO 8601 tarih saati değil

Datetımediff her zaman işaretli bir tamsayı değeri döndürür ve zaman aralığı ölçümü değil, geçen DateTimePart sınırları sayısının bir ölçümüdür.

## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, ve arasında çapraz gün sınırları sayısını hesaplar `2020-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

Aşağıdaki örnek, ve arasındaki çapraz yıl sınırları sayısını hesaplar `2028-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

Aşağıdaki örnek, ve arasında çapraz saat sınırları sayısını hesaplar `2020-01-01T01:00:00.1234527Z` `2020-01-01T01:59:59.1234567Z` . Bu tarih saat değerleri 0,99 saat dışında olsa da, `DateTimeDiff` hiçbir saat sınırı aşılmadığı için 0 değerini döndürür.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
