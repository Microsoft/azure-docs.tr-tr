---
title: Azure Cosmos DB FROM yan tümcesi
description: Azure Cosmos DB için SQL FROM yan tümcesi hakkında bilgi edinin
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326989"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Azure Cosmos DB FROM yan tümcesi

Kaynak filtrelenmez veya sorgu daha sonra yansıtılmamışsa FROM (`FROM <from_specification>`) yan tümcesi isteğe bağlıdır. @No__t-0 gibi bir sorgu `Families` kapsayıcısının tamamına göre sıralar. Kapsayıcı adını kullanmak yerine kapsayıcı için özel tanımlayıcı kökünü de kullanabilirsiniz.

FROM yan tümcesi sorgu başına aşağıdaki kuralları uygular:

* Kapsayıcı `SELECT f.id FROM Families AS f` veya yalnızca `SELECT f.id FROM Families f` gibi diğer ad olabilir. Burada `f` `Families` ' in diğer adıdır. , Tanımlayıcı [diğer adı](sql-query-aliasing.md) için isteğe bağlı bir anahtar sözcüktür.  

* Diğer ad eklendikten sonra özgün kaynak adı bağlanamaz. Örneğin `SELECT Families.id FROM Families f`, `Families` tanımlayıcısının diğer adı olduğu ve artık çözümlenemediği için sözdizimsel olarak geçersiz.  

* Kesin şema bağlılığı yokluğunda belirsiz bağlamalardan kaçınmak için, tüm başvurulan özellikler tam olarak nitelenmelidir. Örneğin, `id` özelliği bağlantılı olmadığından `SELECT id FROM Families f` sözdizimsel olarak geçersizdir.

## <a name="syntax"></a>Sözdizimi
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Bağımsız Değişkenler
  
- `<from_source>`  
  
  Diğer adı olan veya olmayan bir veri kaynağını belirtir. Diğer ad belirtilmemişse, aşağıdaki kurallar kullanılarak `<container_expression>` ' dan çıkarsanacaktır:  
  
  -  İfade bir container_name ise, container_name bir diğer ad olarak kullanılacaktır.  
  
  -  İfade `<container_expression>` ise, property_name bir diğer ad olarak kullanılır. İfade bir container_name ise, container_name bir diğer ad olarak kullanılacaktır.  
  
- @No__t olarak-0  
  
  @No__t-0 ' ın, temel alınan kapsayıcı ifadesinin döndürdüğü bir değer kümesi olduğunu belirtir.  
 
- `input_alias`  
  
  @No__t-0 ' ın, temel alınan kapsayıcı ifadesi tarafından döndürülen her bir dizinin tüm dizi öğeleri üzerinde yinelenerek elde edilen değer kümesini temsil etmesi gerektiğini belirtir. Bir dizi olmayan temeldeki kapsayıcı ifadesi tarafından döndürülen herhangi bir değer yok sayılır.  
  
- `<container_expression>`  
  
  Belgeleri almak için kullanılacak kapsayıcı ifadesini belirtir.  
  
- `ROOT`  
  
  Belgenin varsayılan, bağlı olan kapsayıcıdan alınması gerektiğini belirtir.  
  
- `container_name`  
  
  Belgenin, belirtilen kapsayıcıdan alınması gerektiğini belirtir. Kapsayıcının adı, şu anda bağlı olan kapsayıcının adıyla eşleşmelidir.  
  
- `input_alias`  
  
  Belgenin, belirtilen diğer ad tarafından tanımlanan diğer kaynaktan alınması gerektiğini belirtir.  
  
- `<container_expression> '.' property_`  
  
  Belirtilen kapsayıcı ifadesi tarafından alınan tüm belgeler için `property_name` özelliğine veya array_index Array öğesine erişerek belgenin alınması gerektiğini belirtir.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Belirtilen kapsayıcı ifadesi tarafından alınan tüm belgeler için `property_name` özelliğine veya array_index Array öğesine erişerek belgenin alınması gerektiğini belirtir.  
  
## <a name="remarks"></a>Açıklamalar
  
@No__t-0s) tarafından belirtilen veya çıkarılan tüm diğer adlar benzersiz olmalıdır. @No__t-0property_adı sözdizimi `<container_expression>' ['"property_name"']'` ile aynıdır. Ancak, bir özellik adı bir tanımlayıcı olmayan karakter içeriyorsa, ikinci sözdizimi kullanılabilir.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Eksik özellikleri işleme, eksik dizi öğeleri ve tanımsız değerler
  
Bir kapsayıcı ifadesi özelliklere veya dizi öğelerine erişirse ve bu değer yoksa, bu değer yok sayılır ve daha fazla işlenmez.  
  
### <a name="container-expression-context-scoping"></a>Kapsayıcı ifadesi bağlam kapsamı  
  
Kapsayıcı ifadesi kapsayıcı kapsamlı veya belge kapsamlı olabilir:  
  
-   Kapsayıcı ifadesinin temeldeki kaynağı kök veya `container_name` ise, bir ifade kapsayıcı kapsamıdır. Böyle bir ifade, doğrudan kapsayıcıdan alınan bir belge kümesini temsil eder ve diğer kapsayıcı ifadelerinin işlenmesine bağlı değildir.  
  
-   Bir ifade, kapsayıcı ifadesinin temeldeki kaynağı sorguda daha önceden tanıtılan `input_alias` ise belge kapsamıdır. Böyle bir ifade, diğer ad kapsayıcında ilişkili olan her belge kapsamındaki kapsayıcı ifadesi hesaplanarak elde edilen bir belge kümesini temsil eder.  Sonuç kümesi, temel alınan küme içindeki her belge için kapsayıcı ifadesi hesaplanarak elde edilen bir küme birleşimidir. 

## <a name="examples"></a>Örnekler

### <a name="get-subitems-by-using-the-from-clause"></a>FROM yan tümcesini kullanarak alt öğeleri Al

FROM yan tümcesi, kaynağı daha küçük bir alt kümeye düşürebilir. Her bir öğedeki yalnızca bir alt ağacı numaralandırmak için, aşağıdaki örnekte gösterildiği gibi alt kök kaynak olabilir:

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
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
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

Yukarıdaki sorgu kaynak olarak bir dizi kullandı, ancak kaynak olarak bir nesnesi de kullanabilirsiniz. Sorgu, sonuca eklenmek üzere kaynakta geçerli, tanımlı herhangi bir JSON değerini dikkate alır. Aşağıdaki örnek, bir `address.state` değeri olmayan `Families` ' yı dışarıda bırakır.

```sql
    SELECT *
    FROM Families.address.state
```

Sonuçlar şunlardır:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Başlarken](sql-query-getting-started.md)
- [SELECT yan tümcesi](sql-query-select.md)
- [WHERE yan tümcesi](sql-query-where.md)