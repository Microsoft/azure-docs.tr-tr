---
title: Azure Cosmos DB sorgu dilinde ACOS
description: Azure Cosmos DB 'de SQL sistem işlevi ACOS hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c9d29fba6b5dc55a98bf90cfafe0940d7bf9674
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348734"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Kosinüsü belirtilen sayısal ifadesidir radyan cinsinden açı döndürür; arkkosinüsünü olarak da adlandırılır.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*numeric_expr*  
   Sayısal bir ifadedir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Sayısal bir ifade döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek,-1 `ACOS` döndürür.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Matematik işlevleri Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
