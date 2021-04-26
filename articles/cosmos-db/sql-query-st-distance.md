---
title: Azure Cosmos DB sorgu dilinde ST_DISTANCE
description: Azure Cosmos DB ST_DISTANCE SQL sistem işlevi hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f01f5faf68821fe9f85657c74111efdbb02bd204
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559929"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 İki GeoJSON noktası, Çokgen, MultiPolygon veya LineString ifadeleri arasındaki mesafeyi döndürür. Daha fazla bilgi edinmek için bkz. [Jeo-uzamsal ve GeoJSON konum verileri](sql-query-geospatial-intro.md) makalesi.
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*spatial_expr*  
   Geçerli bir GeoJSON noktası, çokgen veya LineString nesne ifadesi.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Mesafeyi içeren sayısal bir ifade döndürür. Bu, varsayılan başvuru sistemi için ölçü cinsinden ifade edilir.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, yerleşik işlevi kullanılarak belirtilen konumun 30 km içinde olan tüm aile belgelerinin nasıl döndürülyapılacağını gösterir `ST_DISTANCE` . .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, toplamaların bulunduğu sorgular haricinde [Jeo-uzamsal dizinden](index-policy.md#spatial-indexes) faydalanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Uzamsal işlevler Azure Cosmos DB](sql-query-spatial-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
