---
title: Azure Cosmos DB ORDER BY yan tümcesi
description: Azure Cosmos DB için SQL ORDER BY yan tümcesi hakkında bilgi edinin. SQL 'i Azure Cosmos DB JSON sorgu dili olarak kullanın.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338314"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB ORDER BY yan tümcesi
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

İsteğe bağlı `ORDER BY` yan tümce, sorgu tarafından döndürülen sonuçlar için sıralama düzenini belirtir.

## <a name="syntax"></a>Söz dizimi
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Bağımsız değişkenler
  
- `<sort_specification>`  
  
   Sorgu sonuç kümesinin sıralaması yapılacak bir özellik veya ifade belirtir. Bir sıralama sütunu, ad veya özellik diğer adı olarak belirtilebilir.  
  
   Birden çok özellik belirtilebilir. Özellik adları benzersiz olmalıdır. Yan tümcesindeki sıralama özelliklerinin sırası, `ORDER BY` sıralanmış sonuç kümesinin organizasyonunu tanımlar. Diğer bir deyişle, sonuç kümesi ilk özelliğe göre sıralanır ve ardından sıralı liste ikinci özelliğe göre sıralanır ve bu şekilde devam eder.  
  
   Yan tümcesinde başvurulan özellik adları, `ORDER BY` Select listesindeki bir özelliğe ya da `FROM` herhangi bir belirsizlikleri olmadan yan tümcesinde belirtilen koleksiyonda tanımlanan bir özelliğe karşılık gelmelidir.  
  
- `<sort_expression>`  
  
   Sorgu sonuç kümesinin sıralaması yapılacak bir veya daha fazla özelliği veya ifadeyi belirtir.  
  
- `<scalar_expression>`  
  
   Ayrıntılar için [skaler ifadeler](sql-query-scalar-expressions.md) bölümüne bakın.  
  
- `ASC | DESC`  
  
   Belirtilen sütundaki değerlerin artan veya azalan sırada sıralanması gerektiğini belirtir. `ASC` En düşük değerden en yüksek değere göre sıralar. `DESC` en yüksek değerden en düşük değere göre sıralar. `ASC` Varsayılan sıralama düzeni. Null değerler mümkün olan en düşük değer olarak değerlendirilir.  
  
## <a name="remarks"></a>Açıklamalar  
  
   `ORDER BY`Yan tümcesi, dizin oluşturma ilkesinin sıralanmakta olan alanlar için bir dizin içermesini gerektirir. Azure Cosmos DB sorgusu çalışma zamanı, hesaplanan özelliklere karşı değil, özellik adına göre sıralamayı destekler. Azure Cosmos DB birden çok `ORDER BY` özelliği destekler. Bir sorguyu birden çok sıra özellikleriyle çalıştırmak için, sıralanan alanlarda bir [bileşik dizin](index-policy.md#composite-indexes) tanımlamanız gerekir.

> [!Note]
> Sıralanan özellikler bazı belgeler için tanımsız olabilir ve bunları bir SıRALAMA sorgusuna almak istiyorsanız, bu yolu dizine açıkça eklemeniz gerekir. Varsayılan dizin oluşturma ilkesi, sıralama özelliğinin tanımsız olduğu belgelerin alınmasına izin vermez. [Bazı eksik alanları olan belgelerde örnek sorguları gözden geçirin](#documents-with-missing-fields).

## <a name="examples"></a>Örnekler

Örneğin, aileleri, yerleşik şehir adının artan sırada alan bir sorgu aşağıda verilmiştir:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Sonuçlar:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Aşağıdaki sorgu, `id` öğe oluşturma tarihleri sırasına göre aile öğelerini alır. Öğe, `creationDate` *Dönem süresini* temsil eden bir sayı veya 1 Ocak 1970 ' den beri saniye cinsinden geçen süreyi temsil eder.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Sonuçlar:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Ayrıca, birden çok özelliğe göre sıralama yapabilirsiniz. Birden çok özelliğe göre sipariş eden bir sorgu [bileşik dizin](index-policy.md#composite-indexes)gerektirir. Şu sorguyu inceleyin:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Bu sorgu, ailenizi `id` Şehir adının artan sırasına göre alır. Birden çok öğe aynı şehir adına sahip ise, sorgu `creationDate` azalan sırada sıraya alınır.

## <a name="documents-with-missing-fields"></a>Eksik alanları olan belgeler

`ORDER BY`Varsayılan dizin oluşturma ilkesiyle kapsayıcılara karşı çalıştırılan sorgular, Sort özelliğinin tanımsız olduğu belgeleri döndürmeyecektir. Sıralama özelliğinin tanımsız olduğu belgeleri eklemek isterseniz, bu özelliği dizin oluşturma ilkesine açıkça eklemeniz gerekir.

Örneğin, aşağıdaki gibi herhangi bir yolu açıkça içermeyen bir dizin oluşturma ilkesiyle bir kapsayıcı aşağıda verilmiştir `"/*"` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Yan tümcesine dahil olan bir sorgu çalıştırırsanız `lastName` `Order By` , sonuçlar yalnızca tanımlanmış bir özelliği olan belgeleri dahil eder `lastName` . İçin açıkça eklenen bir yol tanımlamadık `lastName` , bu nedenle, olmayan tüm belgeler `lastName` sorgu sonuçlarında görünmeyecek.

Aşağıda `lastName` , biri tanımlanmış olmayan iki belgeye göre sıralama yapan bir sorgu verilmiştir `lastName` :

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Sonuçlar yalnızca tanımlı bir belgeyi içerir `lastName` :

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Kapsayıcının dizin oluşturma ilkesini için açıkça bir yol içerecek şekilde güncelleştirdiğimiz takdirde `lastName` sorgu sonuçlarında tanımsız sıralama özelliğine sahip belgeler dahil edeceğiz. Bu skaler değere (ve bundan sonra değil) yol açacak yolu açıkça tanımlamanız gerekir. `?`Özelliği açıkça dizinlemesini `lastName` ve bundan sonra başka iç içe yol olmamasını sağlamak için dizin oluşturma ilkesinde yol tanımınızdaki karakteri kullanmanız gerekir. `Order By`Sorgunuz [bileşik bir dizin](index-policy.md#composite-indexes)kullanıyorsa, sonuçlar her zaman sorgu sonuçlarında tanımsız bir sıralama özelliği olan belgeleri içerecektir.

Sorgu sonuçlarında tanımsız bir belge olmasına izin veren örnek bir dizin oluşturma ilkesi aşağıda verilmiştir `lastName` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Aynı sorguyu yeniden çalıştırırsanız, eksik olan belgeler `lastName` sorgu sonuçlarında önce görünür:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Sonuçlar:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Sıralama düzenini olarak değiştirirseniz `DESC` , eksik olan belgeler `lastName` sorgu sonuçlarında son görünür:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Sonuçlar:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Yalnızca .NET SDK sürümü 3.4.0 veya üzeri, karışık türlerle SıRALAMAYı destekler. Bu nedenle, tanımsız ve tanımlanmış değerlerin birleşimine göre sıralama yapmak istiyorsanız, bu sürümü (veya üzeri) kullanmanız gerekir.

Sonuçlarda farklı türlerin görünme sırasını kontrol edebilirsiniz. Yukarıdaki örnekte, tanımsız değerlerin dize değerlerinden önce nasıl sıralanacağını gösterdi. Bunun yerine, tanımsız değerlerin sıralama düzeni üzerinde daha fazla denetime sahip olmak istiyorsanız, herhangi bir tanımsız özelliği bir "aaaaaaaaa" veya "zzzzzzzz" dize değerinin ilk veya son olması için atayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [Azure Cosmos DB'de dizin oluşturma ilkeleri](index-policy.md)
- [Konum SıNıRı yan tümcesi](sql-query-offset-limit.md)
