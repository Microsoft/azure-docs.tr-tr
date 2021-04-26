---
title: Azure Cosmos DB sorgu dilinde ST_WITHIN
description: Azure Cosmos DB ST_WITHIN SQL sistem işlevi hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9372da349d0ea9169bb59570b7e6dd0e597d1cdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100558239"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Birinci bağımsız değişkende belirtilen GeoJSON nesnesinin (Point, Çokgen, MultiPolygon veya LineString), ikinci bağımsız değişkende GeoJSON (Point, Çokgen, MultiPolygon veya LineString) içinde olup olmadığını gösteren bir Boole ifadesi döndürür.  
  
## <a name="syntax"></a>Söz dizimi
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız değişkenler
  
*spatial_expr*  
   Bir GeoJSON noktası, çokgen veya LineString nesne ifadesi.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Boole değeri döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, kullanarak bir çokgen içindeki tüm aile belgelerinin nasıl bulunacağını gösterir `ST_WITHIN` .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Sonuç kümesini burada bulabilirsiniz.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Açıklamalar

Bu sistem işlevi, toplamaların bulunduğu sorgular haricinde [Jeo-uzamsal dizinden](index-policy.md#spatial-indexes) faydalanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Uzamsal işlevler Azure Cosmos DB](sql-query-spatial-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB'ye giriş](introduction.md)
