---
title: Azure Cosmos DB sorgu dilinde ST_DISTANCE
description: Azure Cosmos DB 'de SQL sistem işlevi ST_DISTANCE hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1c55bac14b3379f29d57bbad36026749089ec0fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349403"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 İki GeoJSON noktası, çokgen veya LineString ifadeler uzaklığı döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*spatial_expr*  
   Tüm geçerli GeoJSON noktası, çokgen veya LineString nesne ifadesidir.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Uzaklık içeren sayısal bir ifade döndürür. Bu varsayılan başvuru sistemi için ölçümleri ifade edilir.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, `ST_DISTANCE` yerleşik işlevini kullanarak belirtilen konumun 30 km dahilinde olan tüm aile belgelerinin nasıl döndürülyapılacağını gösterir. .  
  
```sql
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Uzamsal işlevler Azure Cosmos DB](sql-query-spatial-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
