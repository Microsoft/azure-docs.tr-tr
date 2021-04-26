---
title: Azure Cosmos DB sorgu dilinde TicksToDateTime
description: Azure Cosmos DB 'de SQL sistem işlevi TicksToDateTime hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9adf525f4edb4104978bae32bbbef15ffb131f68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587262"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Belirtilen ticks değerini bir tarih saat değerine dönüştürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Bağımsız değişkenler

*Onay*  

İşaretli sayısal değer, Unix dönemi bu yana geçen 100 nanosaniyelik ticks geçerli sayısıdır. Diğer bir deyişle, 00:00:00 Perşembe, 1 Ocak 1970 ve bu yana geçen 100 nanosaniyelik ticks sayısıdır.

## <a name="return-types"></a>Dönüş türleri

Şu biçimdeki UTC Tarih ve saat ISO 8601 dize değerini döndürür `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
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

Belirtilen ticks değeri geçersizse, TicksToDateTime döndürülür `undefined` .

## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, zaman işaretlerini bir tarih saat değerine dönüştürür:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
