---
title: Azure Cosmos DB sorgu dilinde ST_ISVALID
description: Azure Cosmos DB 'de SQL sistem işlevi ST_ISVALID hakkında bilgi edinin.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349369"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Belirtilen GeoJSON noktası, çokgen veya LineString ifade geçerli olup olmadığını gösteren bir Boole değeri döndürür.  
  
## <a name="syntax"></a>Sözdizimi
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
*spatial_expr*  
   Bir GeoJSON noktası, çokgen veya LineString ifadesi.  
  
## <a name="return-types"></a>Dönüş türleri
  
  Bir Boolean ifadesi döndürür.  
  
## <a name="examples"></a>Örnekler
  
  Aşağıdaki örnek, bir nokta ST_VALID kullanarak geçerli olup olmadığını denetlemek gösterilmektedir.  
  
  Örneğin, bu nokta geçerli değerler [-90, 90] aralığında böylece sorgu döndürür false değil bir enlem değeri vardır.  
  
  Çokgen için GeoJSON belirtimi sağlanan son koordinat çifti kapalı şekli oluşturmak için birinci ile aynı olması gerekir. İçinde bir Çokgen noktalarının saat yönünün tersi düzende belirtilmesi gerekir. Bir çokgenin belirtilen saat yönünde sırayla bölgesinde tersini temsil eder.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Sonuç kümesini burada verilmiştir.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Sonraki adımlar

- [Uzamsal işlevler Azure Cosmos DB](sql-query-spatial-functions.md)
- [Sistem işlevleri Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB giriş](introduction.md)
