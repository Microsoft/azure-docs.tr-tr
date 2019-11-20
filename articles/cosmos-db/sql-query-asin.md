---
title: Azure Cosmos DB sorgu dilinde ASIN
description: Azure Cosmos DB 'deki SQL sistem işlevi hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5a64fc8d8f87d38f001bf2bc9dd581692c97fe64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348500"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Açının sinüsü belirtilen sayısal ifadesidir radyan cinsinden döndürür. Bu arksinüsünü olarak da adlandırılır.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek,-1 `ASIN` döndürür.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
