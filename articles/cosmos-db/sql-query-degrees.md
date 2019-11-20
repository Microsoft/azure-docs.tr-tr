---
title: Azure Cosmos DB sorgu dilindeki Dereceler
description: Azure Cosmos DB 'de SQL sistem işlevi DERECELERI hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c2109c2c11f137b1966741a95d1d0c02895016
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351188"
---
# <a name="degrees-azure-cosmos-db"></a>DERECE (Azure Cosmos DB)
 Karşılık gelen açıyı derece için radyan cinsinden belirtilen bir açı cinsinden döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, PI/2 radyan cinsinden açı dereceye döndürür.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
