---
title: Azure Cosmos DB diziler ve nesnelerle çalışma
description: Azure Cosmos DB diziler ve nesneler oluşturmak için SQL sözdizimini öğrenin. Bu makalede, dizi nesnelerinde işlemler gerçekleştirmek için bazı örnekler de sağlanmaktadır
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493801"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Azure Cosmos DB diziler ve nesnelerle çalışma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL API 'sinin temel bir özelliği dizi ve nesne oluşturma 'dır. Bu belge, [Aile veri kümesi](sql-query-getting-started.md#upload-sample-data)kullanılarak yeniden oluşturulabilen örnekleri kullanır.

Bu veri kümesindeki örnek bir öğe aşağıda verilmiştir:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Diziler

Aşağıdaki örnekte gösterildiği gibi diziler oluşturabilirsiniz:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Sonuçlar:

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

Sonuçlar:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Yineleme

SQL API 'si, FROM kaynağında [ın anahtar sözcüğüyle](sql-query-keywords.md#in) birlikte JSON dizileri üzerinde yineleme desteği sağlar. Aşağıdaki örnekte:

```sql
SELECT *
FROM Families.children
```

Sonuçlar:

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

Sonraki sorgu, kapsayıcıda yineleme gerçekleştirir `children` `Families` . Çıkış dizisi, önceki sorgudan farklı. Bu örnek `children` , sonuçları böler ve sonuçları tek bir diziye düzleştirir:  

```sql
SELECT *
FROM c IN Families.children
```

Sonuçlar:

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

Sonuçlar:

```json
[{
  "givenName": "Lisa"
}]
```

Ayrıca, bir dizi yinelemesinin sonucunu toplayabilirsiniz. Örneğin, aşağıdaki sorgu tüm aileler arasındaki alt öğe sayısını sayar:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Sonuçlar:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Yineleme için ın anahtar sözcüğünü kullanırken, dizi dışında herhangi bir özelliği filtrelemez veya projenizle. Bunun yerine [birleştirmeleri](sql-query-join.md)kullanmanız gerekir.

Ek örnekler için [Azure Cosmos DB diziler ile çalışırken blog gönderimizi](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/)okuyun.

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Birleştirmeler](sql-query-join.md)
