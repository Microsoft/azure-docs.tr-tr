---
title: Azure Cosmos DB sorgu dilinde GetCurrentTicks
description: Azure Cosmos DB 'de SQL sistem işlevi GetCurrentTicks hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524336"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

00:00:00 Perşembe, 1 Ocak 1970 ve bu yana geçen 100-nanosaniyelik ticks sayısını döndürür.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Dönüş türleri

, UNIX dönemi bu yana geçen 100-nanosaniyelik Tick 'in geçerli sayısı olan işaretli bir sayısal değer döndürür. Diğer bir deyişle GetCurrentTicks, 00:00:00 Perşembe, 1 Ocak 1970 100, bu yana geçen nanosaniyelik ticks sayısını döndürür.

## <a name="remarks"></a>Açıklamalar

GetCurrentTicks () belirleyici olmayan bir işlevdir. Döndürülen sonuç UTC 'dir (Eşgüdümlü Evrensel Saat).

> [!NOTE]
> Bu sistem işlevi dizinden yararlanmayacak. Değerleri geçerli saate göre karşılaştırmak gerekirse, sorgu yürütmeden önce geçerli zamanı alın ve yan tümcesinde bu sabit dize değerini kullanın `WHERE` .

## <a name="examples"></a>Örnekler

Aşağıda, zaman işaretleri cinsinden ölçülen geçerli zamanı döndüren bir örnek verilmiştir:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
