---
title: Azure Cosmos DB içindeki GROUP BY yan tümcesi
description: Azure Cosmos DB için GROUP BY yan tümcesi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: d9cafc100ddd4b553577c447e82334e6ee7d1b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545460"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB içindeki GROUP BY yan tümcesi
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

GROUP BY yan tümcesi sorgunun sonuçlarını belirtilen bir veya daha fazla özellik değerine göre böler.

## <a name="syntax"></a>Söz dizimi

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Bağımsız değişkenler

- `<scalar_expression_list>`

   Sorgu sonuçlarını bölmek için kullanılacak ifadeleri belirtir.

- `<scalar_expression>`
  
   Skalar sorgular ve skaler toplamalar dışında herhangi bir skaler ifadeye izin verilir. Her skaler ifadenin en az bir özellik başvurusu içermesi gerekir. Bağımsız ifadelerin veya her ifadenin kardinaliteinin sayısı için bir sınır yoktur.

## <a name="remarks"></a>Açıklamalar
  
  Bir sorgu GROUP BY yan tümcesi kullandığında, SELECT yan tümcesi yalnızca GROUP BY yan tümcesine dahil olan özelliklerin ve sistem işlevlerinin alt kümesini içerebilir. Tek bir istisna, SELECT yan tümcesinde GROUP BY yan tümcesine eklenmeksizin görünebilen [toplama işlevleridir](sql-query-aggregate-functions.md). Ayrıca SELECT yan tümcesine her zaman sabit değerler ekleyebilirsiniz.

  GROUP BY yan tümcesi SELECT, FROM ve WHERE yan tümcesinden sonra ve sınır sınırı tümceciğinden önce olmalıdır. Şu anda GROUP BY ORDER BY yan tümcesiyle birlikte kullanamazsınız, ancak bu planlanmaktadır.

  GROUP BY yan tümcesi aşağıdakilerden hiçbirine izin vermez:
  
- Diğer ad özellikleri veya diğer ad sistemi işlevleri (SELECT yan tümcesinde hala diğer ad kullanımına izin veriliyor)
- Alt Sorgular
- Toplam sistem işlevleri (bunlar yalnızca SELECT yan tümcesinde kullanılabilir)

Toplu sistem işlevine ve alt sorgusuna sahip sorgular `GROUP BY` desteklenmez. Örneğin, aşağıdaki sorgu desteklenmez:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

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
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Bu sorgu, sonuçları bölmek için kullanılan iki ifadeye sahiptir:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Bazı sonuçlar şunlardır:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Bu sorgu GROUP BY yan tümcesinde bir sistem işlevine sahiptir:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Bazı sonuçlar şunlardır:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Bu sorgu, öğe özelliği ifadesinde hem anahtar sözcükleri hem de sistem işlevlerini kullanır:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Sonuçlar:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [SELECT yan tümcesi](sql-query-select.md)
- [Toplama işlevleri](sql-query-aggregate-functions.md)
