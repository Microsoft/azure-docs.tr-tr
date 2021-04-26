---
title: Ort Azure Cosmos DB sorgu dili
description: Azure Cosmos DB 'daki ortalama (ort) SQL sistem işlevi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555566"
---
# <a name="avg-azure-cosmos-db"></a>Ort (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu toplama işlevi, ifadedeki değerlerin ortalamasını döndürür.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
Aşağıdaki örnek ortalama değerini döndürür `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, bir [Aralık dizininden](index-policy.md#includeexclude-strategy)faydalanır. İçindeki herhangi bir bağımsız değişken `AVG` dize, Boole veya null ise, toplam sistem işlevinin tamamı döndürülür `undefined` . Herhangi bir bağımsız değişken bir `undefined` değere sahipse, `AVG` hesaplamayı etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB matematik işlevleri](sql-query-mathematical-functions.md)
- [Azure Cosmos DB sistem işlevleri](sql-query-system-functions.md)
- [Azure Cosmos DB içindeki toplama işlevleri](sql-query-aggregate-functions.md)