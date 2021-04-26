---
title: Azure Cosmos DB sorgu dilinde DateTimeToTicks
description: Azure Cosmos DB 'de SQL sistem işlevi DateTimeToTicks hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336342"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Belirtilen TarihSaat değeri ticks öğesine dönüştürür. Tek bir onay, saniyenin 100 nanosaniye veya 1 10-milimetre Onth sayısını temsil eder. 

## <a name="syntax"></a>Söz dizimi
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Bağımsız değişkenler
  
*Tarih Saat*  
   Biçimde UTC Tarih ve saat ISO 8601 dize değeri `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Dönüş türleri

, UNIX dönemi bu yana geçen 100-nanosaniyelik Tick 'in geçerli sayısı olan işaretli bir sayısal değer döndürür. Diğer bir deyişle, DateTimeToTicks, 00:00:00 Perşembe, 1 Ocak 1970 ve bu yana geçen 100-nanosaniyelik ticks sayısını döndürür.

## <a name="remarks"></a>Açıklamalar

`undefined`DateTime geçerli BIR ıso 8601 tarih saati değilse, DateTimeDateTimeToTicks değeri döndürülür

Bu sistem işlevi dizinden yararlanmayacak.

## <a name="examples"></a>Örnekler

Tick sayısını döndüren bir örnek aşağıda verilmiştir:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Kesirli saniye sayısını belirtmeden onay işareti sayısını döndüren bir örnek aşağıda verilmiştir:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
