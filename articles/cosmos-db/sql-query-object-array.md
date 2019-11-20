---
title: Azure Cosmos DB diziler ve nesnelerle çalışma
description: Azure Cosmos DB için dizi ve nesne oluşturma SQL sözdizimi hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 17a0e4ddf5acd267a4cfbb68c218fe9409a91d57
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003931"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Azure Cosmos DB diziler ve nesnelerle çalışma

Azure Cosmos DB SQL API 'sinin temel bir özelliği dizi ve nesne oluşturma 'dır.

## <a name="arrays"></a>Dizi

Aşağıdaki örnekte gösterildiği gibi diziler oluşturabilirsiniz:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Sonuçlar şunlardır:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

[Alt sorgu](sql-query-subquery.md) sonuçlarından bir dizi oluşturmak için [dizi ifadesini](sql-query-subquery.md#array-expression) de kullanabilirsiniz. Bu sorgu, bir dizideki tüm alt öğelerin verilen benzersiz adlarını alır.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Yineleme

SQL API 'si, FROM kaynağında [ın anahtar sözcüğüyle](sql-query-keywords.md#in) eklenen yeni bir yapı ile JSON dizileri üzerinde yineleme desteği sağlar. Aşağıdaki örnekte:

```sql
    SELECT *
    FROM Families.children
```

Sonuçlar şunlardır:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Sonraki sorgu, `children` `Families` kapsayıcıda yineleme gerçekleştirir. Çıkış dizisi, önceki sorgudan farklı. Bu örnek, `children`sonuçları böler ve sonuçları tek bir diziye düzleştirir:  

```sql
    SELECT *
    FROM c IN Families.children
```

Sonuçlar şunlardır:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Aşağıdaki örnekte gösterildiği gibi, her bir dizi girişi üzerinde daha fazla filtre uygulayabilirsiniz:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Sonuçlar şunlardır:

```json
    [{
      "givenName": "Lisa"
    }]
```

Ayrıca, bir dizi yinelemesinin sonucunu toplayabilirsiniz. Örneğin, aşağıdaki sorgu tüm aileler arasındaki alt öğe sayısını sayar:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Sonuçlar şunlardır:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Birleştirmeler](sql-query-join.md)