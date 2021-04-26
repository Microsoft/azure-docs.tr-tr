---
title: Azure Cosmos DB sorgu dilinde ST_INTERSECTS
description: Azure Cosmos DB ST_INTERSECTS SQL sistem işlevi hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c123446c7d7f654f0e3ace6c9d92983558509c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559942"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Birinci bağımsız değişkende belirtilen GeoJSON nesnesinin (Point, Çokgen, MultiPolygon veya LineString), ikinci bağımsız değişkende GeoJSON (Point, Çokgen, MultiPolygon veya LineString) kesişip kesişmediğini gösteren bir Boole ifadesi döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*spatial_expr*  
   Bir GeoJSON noktası, çokgen veya LineString nesne ifadesi.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Boole değeri döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, verilen poligonun kesiştiği tüm alanların nasıl bulunacağını gösterir.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, toplamaların bulunduğu sorgular haricinde [Jeo-uzamsal dizinden](index-policy.md#spatial-indexes) faydalanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Uzamsal işlevler Azure Cosmos DB](sql-query-spatial-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
