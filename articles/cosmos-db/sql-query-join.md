---
title: Azure Cosmos DB için SQL JOIN sorguları
description: Azure Cosmos DB için SQL sözdizimi ekleme hakkında bilgi edinin.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: d78904fde53da0e800a69d2148a9c4e3acf57307
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494402"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB birleşimler

İlişkisel bir veritabanında, tablolar arasında birleşimler, Normalleştirilmemiş şemalar tasarlamanın mantıksal bir ilişkmalarıdır. Buna karşılık, SQL API 'SI, *kendinden JOIN*'in mantıksal eşdeğeri olan, şema içermeyen öğelerin Normalleştirilmemiş veri modelini kullanır.

İç birleşimler, birleşime katılan kümelerden oluşan tüm çapraz ürünlere yol açabilir. N-Way JOIN 'in sonucu, kayıt kümesindeki her bir değerin birleşime katılan diğer ad kümesiyle ilişkilendirildiği ve diğer yan tümcelerde bu diğer ada başvuruda bulunarak erişilebilen N-element tanımlama gruplarının bir kümesidir.

## <a name="syntax"></a>Sözdizimi

Dil `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`sözdizimini destekler. Bu sorgu, `N` değerleri olan bir dizi tanımlama grubu döndürür. Her tanımlama grubu, tüm kapsayıcı diğer adlarını ilgili kümeleri üzerinde yineleerek oluşturulan değerlere sahiptir. 

Aşağıdaki FROM yan tümcesine göz atalım: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Her bir kaynağın `input_alias1, input_alias2, …, input_aliasN`tanımlamasına izin verin. Bu FROM yan tümcesi bir N-tanımlama grubu kümesi döndürür (N değeri olan tanımlama grubu). Her tanımlama grubu, tüm kapsayıcı diğer adlarını ilgili kümeleri üzerinde yineleerek oluşturulan değerlere sahiptir.  
  
**Örnek 1** -2 kaynak  
  
- `<from_source1>` kapsayıcı kapsamlı olmasına izin verir ve {A, B, C} kümesini temsil eder.  
  
- `<from_source2>` belge kapsamlı başvuru input_alias1 ve kümeleri temsil edelim:  
  
    `input_alias1 = A,` için {1, 2}  
  
    `input_alias1 = B,` için {3}  
  
    `input_alias1 = C,` için {4, 5}  
  
- FROM yan tümcesi `<from_source1> JOIN <from_source2>` aşağıdaki başlıkların oluşmasına neden olur:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Örnek 2** -3 kaynak  
  
- `<from_source1>` kapsayıcı kapsamlı olmasına izin verir ve {A, B, C} kümesini temsil eder.  
  
- `<from_source2>` belge kapsamlı başvuru `input_alias1` ve kümeleri temsil edelim:  
  
    `input_alias1 = A,` için {1, 2}  
  
    `input_alias1 = B,` için {3}  
  
    `input_alias1 = C,` için {4, 5}  
  
- `<from_source3>` belge kapsamlı başvuru `input_alias2` ve kümeleri temsil edelim:  
  
    `input_alias2 = 1,` için {100, 200}  
  
    `input_alias2 = 3,` için {300}  
  
- FROM yan tümcesi `<from_source1> JOIN <from_source2> JOIN <from_source3>` aşağıdaki başlıkların oluşmasına neden olur:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > `<from_source3>` herhangi bir değer döndürmeyen, `input_alias2``input_alias1`diğer değerleri için tanımlama gruplarının bulunmaması.  
  
**Örnek 3** -3 kaynak  
  
- < From_source1 > kapsayıcı kapsamlı olmasına izin verir ve {A, B, C} kümesini temsil eder.  
  
- `<from_source1>` kapsayıcı kapsamlı olmasına izin verir ve {A, B, C} kümesini temsil eder.  
  
- < From_source2 > Belge kapsamlı başvuru input_alias1 ve kümeleri temsil etmesini sağlar:  
  
    `input_alias1 = A,` için {1, 2}  
  
    `input_alias1 = B,` için {3}  
  
    `input_alias1 = C,` için {4, 5}  
  
- `<from_source3>` `input_alias1` kapsamına almasına izin verir ve kümeleri temsil edin:  
  
    `input_alias2 = A,` için {100, 200}  
  
    `input_alias2 = C,` için {300}  
  
- FROM yan tümcesi `<from_source1> JOIN <from_source2> JOIN <from_source3>` aşağıdaki başlıkların oluşmasına neden olur:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Her ikisi de aynı `<from_source1>`kapsamında olduğundan, bu, `<from_source2>` ve `<from_source3>` arasında çapraz ürüne neden oldu.  Bu, 1 değerine sahip olan 4 (2x2) tanımlama grubu ile sonuçlanır. değer B (1x0) ve 2 (2x1) tanımlama gruplarına sahip, C değeri olan 0 tanımlama grubu.  
  
## <a name="examples"></a>Örnekler

Aşağıdaki örneklerde JOIN yan tümcesinin nasıl çalıştığı gösterilmektedir. Bu örnekleri çalıştırmadan önce örnek [Aile verilerini](sql-query-getting-started.md#upload-sample-data)karşıya yükleyin. Aşağıdaki örnekte, kaynak ve boş bir kümeden her öğenin çapraz çarpımı boş olduğundan sonuç boştur:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Sonuç:

```json
    [{
    }]
```

Aşağıdaki örnekte, JOIN iki JSON nesnesi, öğe kökü `id` ve `children` alt kökü arasındaki bir çapraz üründür. `children` bir dizi, `children` dizi olan tek bir kökle uğradığı için, birleşimde geçerli değildir. Sonuç yalnızca iki sonuç içerir, çünkü dizi ile her öğenin çapraz çarpımı yalnızca tek bir öğe oluşturur.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Sonuçlar şunlardır:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Aşağıdaki örnekte daha geleneksel bir JOIN gösterilmektedir:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Sonuçlar şunlardır:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN yan tümcesinin KIMDEN kaynağı bir yineleyici. Bu nedenle, önceki örnekteki akış:  

1. Dizideki her bir alt öğe `c` genişletin.
2. Öğe `f` köküne sahip bir çapraz ürün uygulama, her bir alt öğe ile düzleştirilmiş `c`.
3. Son olarak, kök nesne `f` `id` özelliğini tek başına proje.

İlk öğe `AndersenFamily`, yalnızca bir `children` öğesi içerir, bu nedenle sonuç kümesi yalnızca tek bir nesne içerir. İkinci öğe olan `WakefieldFamily`iki `children`içerir, bu nedenle, iki nesne de her `children` öğesi için bir tane olmak üzere iki nesne üretir. Bu öğelerin her ikisinde de, bir çapraz üründe bekledikleri gibi, kök alanları aynıdır.

JOIN yan tümcesinin gerçek yardımcı programı, başka bir şekilde proje için zor olan bir şekildeki çapraz üründen tanımlama grupları oluşturmak için kullanılır. Aşağıdaki örnek, kullanıcının tanımlama grupları tarafından karşılanan bir koşul seçmesini sağlayan bir tanımlama grubu birleşimine filtre uygular.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Sonuçlar şunlardır:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Önceki örneğin aşağıdaki uzantısı bir Double JOIN gerçekleştirir. Geçici ürünü aşağıdaki sözde kod olarak görüntüleyebilirsiniz:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily`, tek bir evcil hayvan içeren bir alt öğeye sahiptir, bu nedenle çapraz ürün bu aileden bir satır (1\*1\*1) verir. `WakefieldFamily` iki çocuğu vardır, ancak bu alt öğe iki Evcil hayvan 'a sahiptir. Bu aileye yönelik çapraz ürün 1\*1\*2 = 2 satır verir.

Sonraki örnekte, `pet`üzerinde ek bir filtre vardır ve bu, Evcil hayvan adının `Shadow`olmadığı tüm başlıkları dışlar. Dizilerden tanımlama grupları oluşturabilir, kayıt düzeni öğelerinin herhangi birine filtre uygulayabilir ve öğelerin herhangi bir birleşimini proje aracılığıyla yapabilirsiniz.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Sonuçlar şunlardır:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlangıç](sql-query-getting-started.md)
- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Alt](sql-query-subquery.md)