---
title: Azure Cosmos DB sorgu dilinde Datetimefrompsanat
description: Azure Cosmos DB 'de SQL sistem işlevi Datetimefrompsanat hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b0e7996b71e68db371201da1f0f5c93486ae4e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592107"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>Datetimefrompsanat (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Giriş değerlerinden oluşturulan bir dize tarih saat değeri döndürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Bağımsız değişkenler
  
*Numberyear* Biçimde yılın tamsayı değeri `YYYY`

*numberMonth*  
   Biçimde ayın tamsayı değeri `MM`

*numberDay*  
   Biçimin gün için tamsayı değeri `DD`

Sayı biçiminde saat için *Numberhour* (isteğe bağlı) tamsayı değeri`hh`

Sayı biçimindeki dakika için *Numberminute* (isteğe bağlı) tamsayı değeri`mm`

saniyenin ikinci değeri için *numbersecond* (isteğe bağlı) tamsayı değeri`ss`

bir saniyenin kesirli kısmı için *Numberoffkctionsofsecond* (isteğe bağlı) sayı değeri`.fffffff`

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
  
 ISO 8601 biçimi hakkında daha fazla bilgi için bkz. [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Açıklamalar

Belirtilen tamsayılar geçersiz bir tarih saat oluşturacaksa, Datetimefrompsanatı döndürülür `undefined` .

İsteğe bağlı bir bağımsız değişken belirtilmemişse, değeri 0 olur.

## <a name="examples"></a>Örnekler

İşte yalnızca bir tarih saat oluşturmak için gerekli bağımsız değişkenleri içeren bir örnek:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Aşağıda, bir tarih saat oluşturmak için bazı isteğe bağlı bağımsız değişkenler de kullanan başka bir örnek verilmiştir:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Aşağıda, bir tarih saat oluşturmak için tüm isteğe bağlı bağımsız değişkenleri de kullanan başka bir örnek verilmiştir:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
