---
title: Azure Cosmos DB sorgu dilinde GetCurrentTimestamp
description: Azure Cosmos DB 'de SQL sistem işlevi GetCurrentTimestamp hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524278"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 00:00:00 Perşembe, 1 Ocak 1970 tarihinden itibaren geçen milisaniye sayısını döndürür.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Dönüş türleri
  
İşaretli bir sayısal değeri döndürür. bu yana geçen milisaniye sayısı, örneğin, UNIX dönemi (00:00:00, 1 Ocak 1970).

## <a name="remarks"></a>Açıklamalar

GetCurrentTimestamp () belirleyici olmayan bir işlevdir. Döndürülen sonuç UTC 'dir (Eşgüdümlü Evrensel Saat).

> [!NOTE]
> Bu sistem işlevi dizinden yararlanmayacak. Değerleri geçerli saate göre karşılaştırmak gerekirse, sorgu yürütmeden önce geçerli zamanı alın ve yan tümcesinde bu sabit dize değerini kullanın `WHERE` .

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
- [Azure Cosmos DB'ye giriş](introduction.md)
