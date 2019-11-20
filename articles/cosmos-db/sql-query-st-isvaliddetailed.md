---
title: Azure Cosmos DB sorgu dilinde ST_ISVALIDDETAILED
description: Azure Cosmos DB 'de SQL sistem işlevi ST_ISVALIDDETAILED hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349346"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Bir Boole değeri içeren bir JSON değeri, belirtilen GeoJSON noktası, çokgen veya LineString ifade geçerliyse ve geçersiz değeri döndürür, ayrıca bir dize değeri olarak nedeni.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*spatial_expr*  
   Bir GeoJSON noktası ya da çokgen ifadesi.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir Boole değeri içeren bir JSON değeri, belirtilen GeoJSON noktası veya Çokgen ifade geçerliyse ve geçersiz değeri döndürür, ayrıca bir dize değeri olarak nedeni.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `ST_ISVALIDDETAILED` kullanarak geçerliliği denetleme (ayrıntılar ile).  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Uzamsal işlevler Azure Cosmos DB](sql-query-spatial-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
