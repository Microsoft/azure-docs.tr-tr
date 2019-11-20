---
title: Azure Cosmos DB içindeki GROUP BY yan tümcesi
description: Azure Cosmos DB için GROUP BY yan tümcesi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819099"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB içindeki GROUP BY yan tümcesi

GROUP BY yan tümcesi sorgunun sonuçlarını belirtilen bir veya daha fazla özellik değerine göre böler.

> [!NOTE]
> Azure Cosmos DB Şu anda, .NET SDK 3,3 ve üzeri sürümlerde ve JavaScript SDK 3,4 ve üzeri sürümlerde grubunu desteklemektedir.
> Diğer dil SDK desteği şu anda kullanılamıyor ancak planlanmaktadır.

## <a name="syntax"></a>Sözdizimi

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Bağımsız Değişkenler

- `<scalar_expression_list>`

   Sorgu sonuçlarını bölmek için kullanılacak ifadeleri belirtir.

- `<scalar_expression>`
  
   Skalar sorgular ve skaler toplamalar dışında herhangi bir skaler ifadeye izin verilir. Her skaler ifadenin en az bir özellik başvurusu içermesi gerekir. Bağımsız ifadelerin veya her ifadenin kardinaliteinin sayısı için bir sınır yoktur.

## <a name="remarks"></a>Açıklamalar
  
  Bir sorgu GROUP BY yan tümcesi kullandığında, SELECT yan tümcesi yalnızca GROUP BY yan tümcesine dahil olan özelliklerin ve sistem işlevlerinin alt kümesini içerebilir. Tek bir istisna, SELECT yan tümcesinde GROUP BY yan tümcesine eklenmeksizin görünebilen [toplu sistem işlevleridir](sql-query-aggregates.md). Ayrıca SELECT yan tümcesine her zaman sabit değerler ekleyebilirsiniz.

  GROUP BY yan tümcesi SELECT, FROM ve WHERE yan tümcesinden sonra ve sınır sınırı tümceciğinden önce olmalıdır. Şu anda GROUP BY ORDER BY yan tümcesiyle birlikte kullanamazsınız, ancak bu planlanmaktadır.

  GROUP BY yan tümcesi aşağıdakilerden hiçbirine izin vermez:
  
- Diğer ad özellikleri veya diğer ad sistemi işlevleri (SELECT yan tümcesinde hala diğer ad kullanımına izin veriliyor)
- Alt
- Toplam sistem işlevleri (bunlar yalnızca SELECT yan tümcesinde kullanılabilir)

## <a name="examples"></a>Örnekler

Bu örnekler, [Azure Cosmos db sorgu deneme alanı](https://www.documentdb.com/sql/demo)aracılığıyla kullanılabilen beslenme veri kümesini kullanır.

Örneğin, her bir, her bir Mdgroup 'taki öğelerin toplam sayısını döndüren bir sorgu aşağıda verilmiştir:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Bazı sonuçlar (sonuçları sınırlandırmak için TOP anahtar sözcüğü kullanılır):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Bu sorgu, sonuçları bölmek için kullanılan iki ifadeye sahiptir:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Bazı sonuçlar şunlardır:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Bu sorgu GROUP BY yan tümcesinde bir sistem işlevine sahiptir:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Bazı sonuçlar şunlardır:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Bu sorgu, öğe özelliği ifadesinde hem anahtar sözcükleri hem de sistem işlevlerini kullanır:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Sonuçlar şunlardır:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlangıç](sql-query-getting-started.md)
- [SELECT yan tümcesi](sql-query-select.md)
- [Toplama işlevleri](sql-query-aggregates.md)
