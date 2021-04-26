---
title: Azure Cosmos DB sorgu dilinde MIN
description: Azure Cosmos DB en düşük (MIN) SQL sistem işlevi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555564"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu toplama işlevi, ifadedeki değerlerin en küçük değerini döndürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*scalar_expr*  
   Skaler bir ifadedir. 
  
## <a name="return-types"></a>Dönüş türleri
  
Skaler bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, en küçük değerini döndürür `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, bir [Aralık dizininden](index-policy.md#includeexclude-strategy)faydalanır. İçindeki bağımsız değişkenler `MIN` sayı, dize, Boole veya null olabilir. Tanımsız değerler yok sayılır.

Farklı tür verilerini karşılaştırırken, aşağıdaki öncelik sırası kullanılır (artan sırada):

- null
- boolean
- sayı
- string

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB matematik işlevleri](sql-query-mathematical-functions.md)
- [Azure Cosmos DB sistem işlevleri](sql-query-system-functions.md)
- [Azure Cosmos DB içindeki toplama işlevleri](sql-query-aggregate-functions.md)