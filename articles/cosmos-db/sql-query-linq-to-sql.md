---
title: Azure Cosmos DB LINQ to SQL çevirisi
description: LINQ sorgularını Azure Cosmos DB SQL sorgularıyla eşleme.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: ad6dcf7307955300a781a7a649b6ac76b3c69589
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003548"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL çeviri

Azure Cosmos DB sorgu sağlayıcısı, bir LINQ sorgusundan Cosmos DB SQL sorgusuna en iyi çaba eşlemesini gerçekleştirir. Aşağıdaki açıklamada, LINQ ile ilgili temel bir benzerlik varsayılmaktadır.

Sorgu sağlayıcısı tür sistemi yalnızca JSON ilkel türlerini destekler: numeric, Boolean, String ve null.

Sorgu sağlayıcısı aşağıdaki skaler ifadeleri destekler:

- Sorgu değerlendirme sırasında temel veri türlerinin sabit değerleri de dahil olmak üzere sabit değerler.
  
- Bir nesnenin veya dizi öğesinin özelliğine başvuran Özellik/dizi dizini ifadeleri. Örneğin:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Sayısal ve Boole değerlerinde ortak aritmetik ifadeler de dahil olmak üzere aritmetik ifadeler. Tüm liste için [Azure Cosmos db SQL belirtimine](https://go.microsoft.com/fwlink/p/?LinkID=510612)bakın.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Bir dize değerini bir sabit dize değerine karşılaştırmayı içeren dize karşılaştırma ifadeleri.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Bileşik değer türü veya anonim türdeki bir nesneyi ya da bu nesnelerden oluşan bir diziyi döndüren nesne/dizi oluşturma ifadeleri. Bu değerleri iç içe geçirebilirsiniz.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Desteklenen LINQ işleçleri

SQL .NET SDK 'sına dahil edilen LINQ sağlayıcısı aşağıdaki işleçleri destekler:

- Şunu **seçin**: Tahminler, nesne oluşturma dahil olmak üzere SQL SELECT 'e çevirmelidir.
- **Burada**: Filtreler SQL 'e çevirir ve SQL işleçleri arasındaki `&&` `||` `!` çeviriyi destekler
- **SelectMany**: Dizileri SQL JOIN yan tümcesine geri sarma olanağı sağlar. Dizi öğelerinde filtrelemek için ifadeleri zincirlemek veya iç içe yerleştirmek için kullanın.
- **OrderBy** ve **OrderByDescending**: ASC veya DESC ile ORDER 'a çevirin.
- **Sayısı**, **toplam**, **Min**, **Max**, ve **ortalama** işleçleri toplama ve zaman uyumsuz eşdeğerlerine **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, ve **AverageAsync**.
- **CompareTo**: Aralık karşılaştırmaları ' ne çevirir. Genellikle dizeler için kullanılır, çünkü .NET ' de karşılaştırılabilir değildir.
- Şunları **yapın**: Bir sorgudaki sonuçları kısıtlamak için SQL üst öğesine çevrilir.
- **Matematik işlevleri**: `Abs`.Net ,`Exp` ,,`Pow`,,,,,,,,,,,,, `Acos` `Asin` `Atan` `Ceiling` `Cos` `Floor` `Log` `Log10` `Round` ,,,`Truncate` , ve ile eşdeğer SQL yerleşik işlevleri. `Sign` `Sin` `Sqrt` `Tan`
- **Dize işlevleri**: `Concat`.Net ,`Reverse` ,,`ToUpper`,,,,,,,,,,,,, `Contains` `Count` `EndsWith``IndexOf` `Replace` `StartsWith` `SubString` `ToLower` `TrimEnd`, ve `TrimStart` eşdeğer SQL yerleşik işlevlerine.
- **Dizi işlevleri**: , .Net `Concat`, `Contains`ve ile `Count` eşdeğer SQL yerleşik işlevlerine çeviriyi destekler.
- **Jeo-uzamsal uzantı işlevleri**: ,, `Distance` `IsValidDetailed`, `IsValid` Ve`Within` ' nin eşdeğer SQL yerleşik işlevlerine çevirisini destekler.
- **Kullanıcı tanımlı Işlev uzantısı işlevi**: Saplama yönteminden `UserDefinedFunctionProvider.Invoke` karşılık gelen Kullanıcı tanımlı işleve çeviriyi destekler.
- **Çeşitli**: `Coalesce` Ve koşullu işleçlerin çevirisini destekler. , Bağlama `Contains` göre ' de CONTAINS, ARRAY_CONTAINS veya SQL dize ile çeviri yapabilir.

## <a name="examples"></a>Örnekler

Aşağıdaki örneklerde, bazı standart LINQ sorgu işleçlerinin Cosmos DB sorgulara nasıl çevrilebileceğini gösterir.

### <a name="select-operator"></a>İşleç seçin

Söz dizimi `input.Select(x => f(x))`burada `f` skaler bir ifade.

**İşleç seç, örnek 1:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**İşleç seç, örnek 2:** 

- **LINQ lambda ifadesi**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**İşleç seç, örnek 3:**

- **LINQ lambda ifadesi**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany işleci

Söz dizimi `input.SelectMany(x => f(x))`burada `f` bir kapsayıcı türü döndüren bir skaler ifade.

- **LINQ lambda ifadesi**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Burada işleci

Söz dizimi `input.Where(x => f(x))`burada `f` bir Boole değeri döndüren bir skaler ifade.

**WHERE işleci, örnek 1:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**WHERE işleci, örnek 2:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Bileşik SQL sorguları

Daha güçlü sorgular oluşturmak için önceki işleçleri oluşturabilirsiniz. Cosmos DB iç içe kapsayıcıları desteklediğinden, kompozisyonu birleştirebilir veya iç içe yerleştirebilirsiniz.

### <a name="concatenation"></a>Birleştirme

Söz dizimi `input(.|.SelectMany())(.Select()|.Where())*`. Art arda eklenmiş bir sorgu, daha sonra `SelectMany` birden çok `Select` veya `Where` işleçle bir isteğe bağlı sorgu ile başlayabilir.

**Birleştirme, örnek 1:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Birleştirme, örnek 2:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Birleştirme, örnek 3:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Birleştirme, örnek 4:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>İç içe geçirme

`input.SelectMany(x=>x.Q())` Sözdizimi ,,veya`Where` işleçtir. `SelectMany` `Q` `Select`

İç içe sorgu, iç sorguyu dış kapsayıcının her öğesine uygular. Önemli bir özellik, iç sorgunun bir kendi kendine birleşim gibi dış kapsayıcıdaki öğelerin alanlarına başvurabileceği bir özelliktir.

**İç içe, örnek 1:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**İç içe, örnek 2:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**İç içe, örnek 3:**

- **LINQ lambda ifadesi**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Belge verilerini modelleme](modeling-data.md)
