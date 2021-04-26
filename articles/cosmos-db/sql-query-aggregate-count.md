---
title: Azure Cosmos DB sorgu dilinde say
description: Azure Cosmos DB sayı (COUNT) SQL sistem işlevi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553406"
---
# <a name="count-azure-cosmos-db"></a>SAYı (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu sistem işlevi, ifadedeki değerlerin sayısını döndürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*scalar_expr*  
   Herhangi bir skaler ifade
  
## <a name="return-types"></a>Dönüş türleri
  
Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, bir kapsayıcıdaki toplam öğe sayısını döndürür:
  
```sql
SELECT COUNT(1)
FROM c
``` 
SAYı, herhangi bir skaler ifadeyi giriş olarak alabilir. Aşağıdaki sorgu eşdeğer sonuçlar üretecektir:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, sorgu filtresindeki herhangi bir özellik için bir [Aralık dizininden](index-policy.md#includeexclude-strategy) faydalanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB matematik işlevleri](sql-query-mathematical-functions.md)
- [Azure Cosmos DB sistem işlevleri](sql-query-system-functions.md)
- [Azure Cosmos DB içindeki toplama işlevleri](sql-query-aggregate-functions.md)