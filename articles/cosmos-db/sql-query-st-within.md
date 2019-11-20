---
title: Azure Cosmos DB sorgu dilinde ST_WITHIN
description: Azure Cosmos DB 'de SQL sistem işlevi ST_WITHIN hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40ecb26e7ac782d7831e6ef94c9d3cfc6a370cbb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349338"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 İlk bağımsız değişkende belirtilen GeoJSON nesne (noktası, çokgen veya LineString) ikinci bağımsız değişkende GeoJSON (noktası, çokgen veya LineString) içinde olup olmadığını gösteren bir Boole ifadesi döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*spatial_expr*  
   Bir GeoJSON noktası, çokgen veya LineString nesne ifadesi.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir Boolean değer döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `ST_WITHIN` kullanarak bir çokgen içindeki tüm aile belgelerinin nasıl bulunacağını gösterir.  
  
```sql
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Uzamsal işlevler Azure Cosmos DB](sql-query-spatial-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
