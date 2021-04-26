---
title: Azure Cosmos DB sorgu dilinde TimestampToDateTime
description: Azure Cosmos DB 'de SQL sistem işlevi Timestamptotarihsaat hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: e9a77ab0ac32d627d59e2cb0fa4a680f174a6833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587245"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>Timestamptotarihsaat (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Belirtilen zaman damgası değerini bir tarih saat değerine dönüştürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>Bağımsız değişkenler

*İlişkin*  

İşaretli bir sayısal değer, UNIX dönemi bu yana geçen milisaniye cinsinden geçerli süre. Diğer bir deyişle, 00:00:00 Perşembe, 1 Ocak 1970 tarihinden itibaren geçen milisaniye sayısı.

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

`undefined`Belirtilen zaman damgası değeri geçersiz Ise TimestampToDateTime döndürülür.

## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, zaman damgasını bir tarih saat değerine dönüştürür:

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
