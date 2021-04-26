---
title: Azure Cosmos DB sorgu dilinde toplam
description: Azure Cosmos DB içindeki Sum (SUM) SQL sistem işlevi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553430"
---
# <a name="sum-azure-cosmos-db"></a>Toplam (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu toplama işlevi, ifadedeki değerlerin toplamını döndürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek, öğesinin toplamını döndürür `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, bir [Aralık dizininden](index-policy.md#includeexclude-strategy)faydalanır. İçindeki herhangi bir bağımsız değişken `SUM` dize, Boole veya null ise, toplam sistem işlevinin tamamı döndürülür `undefined` . Herhangi bir bağımsız değişkenin `undefined` değeri varsa, `SUM` hesaplamayı etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB matematik işlevleri](sql-query-mathematical-functions.md)
- [Azure Cosmos DB sistem işlevleri](sql-query-system-functions.md)
- [Azure Cosmos DB içindeki toplama işlevleri](sql-query-aggregate-functions.md)