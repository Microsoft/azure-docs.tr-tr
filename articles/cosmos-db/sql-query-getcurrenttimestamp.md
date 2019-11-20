---
title: Azure Cosmos DB sorgu dilinde GetCurrentTimestamp
description: Azure Cosmos DB 'de SQL sistem işlevi GetCurrentTimestamp hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351001"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 00:00:00 Perşembe, 1 Ocak 1970 tarihinden itibaren geçen milisaniye sayısını döndürür. 
  
## <a name="syntax"></a>Sözdizimi
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir sayısal değer döndürür. Bu, UNIX dönemi 'nden beri geçen milisaniye sayısı (00:00:00 Perşembe, 1 Ocak 1970).

## <a name="remarks"></a>Açıklamalar

  GetCurrentTimestamp () belirleyici olmayan bir işlevdir.
  
  Döndürülen sonuç UTC 'dir (Eşgüdümlü Evrensel Saat).

## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, GetCurrentTimestamp () yerleşik işlevini kullanarak geçerli zaman damgasının nasıl alınacağını gösterir.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Örnek bir sonuç kümesi aşağıda verilmiştir.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Sonraki adımlar

- [Tarih ve saat işlevleri Azure Cosmos DB](sql-query-date-time-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
