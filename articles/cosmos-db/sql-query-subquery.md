---
title: Azure Cosmos DB için SQL alt sorguları
description: SQL alt sorguları ve bunların ortak kullanım durumları hakkında bilgi edinin Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: cea9963f5073834a24ede44306eb89414909fc83
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003481"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB için SQL alt sorgu örnekleri

Alt sorgu, başka bir sorgu içinde iç içe geçmiş bir sorgudur. Bir alt sorgu iç sorgu veya iç seçim olarak da adlandırılır. Alt sorgu içeren deyime genellikle dış sorgu denir.

Bu makalede, Azure Cosmos DB 'de SQL alt sorguları ve bunların ortak kullanım durumları açıklanmaktadır. Bu belgedeki tüm örnek sorgular, [Azure Cosmos db sorgu deneme alanı](https://www.documentdb.com/sql/demo)önceden yüklenmiş olan bir beslenme veri kümesi için çalıştırılabilir.

## <a name="types-of-subqueries"></a>Alt sorgu türleri

İki ana alt sorgu türü vardır:

* **Bağıntılı**: Dış sorgudaki değerlere başvuran bir alt sorgu. Alt sorgu, dış sorgunun işlediği her satır için bir kez değerlendirilir.
* **Bağıntılı olmayan**: Dış sorgudan bağımsız bir alt sorgu. Dış sorguya bağlı kalmadan kendi üzerinde çalıştırılabilir.

> [!NOTE]
> Azure Cosmos DB yalnızca bağıntılı alt sorguları destekler.

Alt sorgular, geri dönedikleri satır ve sütun sayısına göre daha fazla sınıflandırılabilir. Üç tür vardır:
* **Tablo**: Birden çok satırı ve birden çok sütunu döndürür.
* **Çoklu değer**: Birden çok satırı ve tek bir sütunu döndürür.
* **Skaler**: Tek bir satırı ve tek bir sütunu döndürür.

Azure Cosmos DB içindeki SQL sorguları her zaman tek bir sütun (basit bir değer ya da karmaşık bir belge) döndürür. Bu nedenle, Azure Cosmos DB yalnızca çok değerli ve skaler alt sorgular geçerlidir. Yalnızca FROM yan tümcesinde ilişkisel ifade olarak bir çok değerli alt sorgu kullanabilirsiniz. Skalar bir alt sorguyu SELECT veya WHERE yan tümcesinde skaler bir ifade olarak veya FROM yan tümcesinde ilişkisel bir ifade olarak kullanabilirsiniz.

## <a name="multi-value-subqueries"></a>Çoklu değer alt sorguları

Çoklu değer alt sorguları bir dizi belge döndürür ve her zaman FROM yan tümcesinde kullanılır. Bunlar için kullanılır:

* JOIN ifadelerini iyileştirme. 
* Pahalı ifadeleri bir kez değerlendirmek ve birden çok kez başvurmak.

## <a name="optimize-join-expressions"></a>JOIN ifadelerini iyileştirme

Birden çok değerli alt sorgular WHERE yan tümcesindeki tüm çapraz birleşimler yerine her bir SELECT-many ifadesinden sonra gelen koşulları ileterek BIRLEŞTIRME ifadelerini iyileştirebilirler.

Aşağıdaki sorguyu göz önünde bulundurun:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Bu sorgu için dizin, "Infant Formula" adlı bir etiketi olan herhangi bir belgeyle eşleşir. Bu, 0 ile 10 arasında bir değer ve 1 ' den büyük olan bir hizmet veren öğe olan bir nutrient öğesidir. Buradaki JOIN ifadesi, herhangi bir filtre uygulanmadan önce, eşleşen her belge için tüm etiket, nutristalar ve servi dizileri öğelerinin çapraz çarpımını gerçekleştirir. 

WHERE yan tümcesi, her < c, t, n, s > demet için filtre koşulunu uygular. Örneğin, eşleşen bir belge üç dizinin her birinde 10 öğe içeriyorsa, 1 x 10 x 10 x 10 (diğer bir deyişle, 1.000) tanımlama gruplarına genişletilir. Burada alt sorguları kullanmak, birleştirilmiş dizi öğelerinin sonraki ifadeyle katılmadan önce filtrelemeye yardımcı olabilir.

Bu sorgu, önceki bir ile eşdeğerdir, ancak alt sorgular kullanır:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Etiketler dizisindeki yalnızca bir öğe filtreyle eşleşen ve hem nutristalar hem de servi dizileri için beş öğe olduğunu varsayalım. Ardından, ilk sorgudaki 1.000 öğeden farklı olarak, JOIN ifadeleri 1 x 1 x 5 x 5 = 25 öğe olacak şekilde genişletilir.

## <a name="evaluate-once-and-reference-many-times"></a>Bir kez değerlendirin ve başvuruyu birçok kez yapın

Alt sorgular, Kullanıcı tanımlı işlevler (UDF 'ler), karmaşık dizeler veya aritmetik ifadeler gibi pahalı ifadelerle sorguları iyileştirmenize yardımcı olabilir. İfadeyi bir kez değerlendirmek ve çok sayıda başvuru yapmak için bir JOIN ifadesiyle birlikte bir alt sorgu kullanabilirsiniz.

Aşağıdaki sorgu UDF `GetMaxNutritionValue` 'i iki kez çalıştırır:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

UDF 'yi yalnızca bir kez çalıştıran eşdeğer bir sorgu aşağıda verilmiştir:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> JOIN ifadelerinin çoklu ürün davranışlarını göz önünde bulundurun. UDF ifadesi tanımsız olarak değerlendiriyorsa, JOIN ifadesinin doğrudan değer yerine alt sorgudan bir nesne döndürerek tek bir satır ürettiğinden emin olmalısınız.
>

İşte bir değer yerine bir nesne döndüren benzer bir örnek:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Yaklaşım UDF 'ler ile sınırlı değildir. Potansiyel olarak pahalı olan herhangi bir ifade için geçerlidir. Örneğin, matematik işleviyle `avg`aynı yaklaşımı kullanabilirsiniz:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Dış başvuru verileriyle birleştirmeyi taklit edin

Genellikle ölçü birimleri veya ülke kodu gibi nadiren değişiklik yapan statik verilere başvurmanız gerekebilir. Her belge için bu verilerin yinelenmemesi daha iyidir. Bu tekrardan kaçınmak, depolama alanına kaydedilecek ve belge boyutunu daha küçük tutarak yazma performansını iyileştirmeyecektir. Bir başvuru verileri koleksiyonuyla iç birleşim semantiğini taklit etmek için bir alt sorgu kullanabilirsiniz.

Örneğin, bu başvuru verileri kümesini göz önünde bulundurun:

| **Birim** | **Name**            | **Çarpanını** | **Taban birim** |
| -------- | ------------------- | -------------- | ------------- |
| denetimini       | Nanogram            | 1,00 E-09       | Durumu          |
| µg       | Mikro gram           | 1,00 E-06       | Durumu          |
| mg       | Miligram           | 1,00 E-03       | Durumu          |
| G        | Durumu                | 1,00 E + 00       | Durumu          |
| başlayarak       | Kilogram            | 1,00 E + 03       | Durumu          |
| mg       | Megagram            | 1,00 E + 06       | Durumu          |
| Gg       | Gigagram            | 1,00 E + 09       | Durumu          |
| nJ       | Nanojoule           | 1,00 E-09       | Joule         |
| µJ       | Mikro Joule          | 1,00 E-06       | Joule         |
| mJ       | Milijoule          | 1,00 E-03       | Joule         |
| K        | Joule               | 1,00 E + 00       | Joule         |
| kJ       | Kilojoule           | 1,00 E + 03       | Joule         |
| MJ       | Megajoule           | 1,00 E + 06       | Joule         |
| GJ       | Gigajoule           | 1,00 E + 09       | Joule         |
| yükseltil      | Calorie             | 1,00 E + 00       | Calorie       |
| kcal     | Calorie             | 1,00 E + 03       | Calorie       |
| IU       | Uluslararası birimler |                |               |


Aşağıdaki sorgu, bu verilerle birleştirme taklit eder; böylece, birimin adını çıktıya ekleyin:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Skaler alt sorgular

Skaler alt sorgu ifadesi tek bir değer değerlendiren bir alt sorgu olur. Skaler alt sorgu ifadesinin değeri, alt sorgunun projeksiyonunun (SELECT yan tümcesinin) değeridir.  Skaler bir alt sorgu ifadesini, skalar bir ifadenin geçerli olduğu birçok yerde kullanabilirsiniz. Örneğin, SELECT ve WHERE yan tümcelerinde herhangi bir ifadede skaler bir alt sorgu kullanabilirsiniz.

Skaler bir alt sorgu kullanılması her zaman iyileştirilerek de yardımcı olmaz. Örneğin, bir sistem veya Kullanıcı tanımlı işlevlere bağımsız değişken olarak skaler bir alt sorgu geçirmek, kaynak birimi (RU) tüketimine veya gecikmede bir avantaj sağlamaz.

Skaler alt sorgular, şu şekilde daha da sınıflandırılabilir:
* Basit ifade skaler alt sorguları
* Skaler alt sorgular toplama

## <a name="simple-expression-scalar-subqueries"></a>Basit ifade skaler alt sorguları

Basit ifade skaler alt sorgusu, hiçbir toplama ifadesi içermeyen bir SELECT yan tümcesine sahip bağıntılı bir alt sorgu olur. Derleyici bunları bir daha büyük basit ifadeye dönüştürdüğünden bu alt sorgular en iyi duruma getirme avantajları sağlamaz. İç ve dış sorgular arasında bağıntılı bağlam yok.

İşte birkaç örnek:

**Örnek 1**

```sql
SELECT 1 AS a, 2 AS b
```

Bu sorguyu, bir basit ifade skaler alt sorgusunu kullanarak yeniden yazabilirsiniz:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Her iki sorgu de bu çıktıyı üretir:

```json
[
  { "a": 1, "b": 2 }
]
```

**Örnek 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Bu sorguyu, bir basit ifade skaler alt sorgusunu kullanarak yeniden yazabilirsiniz:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Sorgu çıkışı:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Örnek 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Bu sorguyu, bir basit ifade skaler alt sorgusunu kullanarak yeniden yazabilirsiniz:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Sorgu çıkışı:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Skaler alt sorgular toplama

Bir toplama skalar alt sorgusu, projeksiyonde tek bir değer değerlendiren bir toplama işlevi olan bir alt sorgu olur.

**Örnek 1:**

Projeksiyonundaki tek bir toplama işlevi ifadesi içeren bir alt sorgu aşağıda verilmiştir:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Sorgu çıkışı:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Örnek 2**

Birden çok toplama işlevi ifadesi içeren bir alt sorgu aşağıda verilmiştir:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Sorgu çıkışı:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Örnek 3**

Hem projeksiyde hem de filtrede bir toplama alt sorgusuna sahip bir sorgu.

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Sorgu çıkışı:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Bu sorguyu yazmanın en iyi yolu, alt sorgu üzerinde birleşmenin yanı sıra SELECT ve WHERE yan tümcelerinde alt sorgu diğer adına başvurmalıdır. Bu sorgu daha verimlidir çünkü alt sorguyu yalnızca JOIN ifadesinde yürütmeniz ve hem projeksiyon hem de filtrede değil.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS ifadesi

Azure Cosmos DB var olan ifadeleri destekler. Bu, SQL API Azure Cosmos DB yerleşik bir toplam skaler alt sorgu. VAR, alt sorgu ifadesi alan ve alt sorgu herhangi bir satır döndürürse true değeri döndüren bir Boolean ifadedir. Aksi takdirde, false döndürür.

Azure Cosmos DB SQL API 'SI, Boolean ifadeleri ve diğer skalar ifadeler arasında ayrım yaptığından, hem SELECT hem de WHERE yan tümcelerinde var ' ı kullanabilirsiniz. Bu, bir Boolean ifadesinin (örneğin,,, ve IÇINDEKI) filtreye sınırlı olduğu T-SQL ' in aksine.

EXISTS alt sorgusu tanımsız tek bir değer döndürürse, var değeri false olarak değerlendirilir. Örneğin, false sonucunu veren aşağıdaki sorguyu göz önünde bulundurun:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Önceki alt sorgudaki VALUE anahtar sözcüğü atlanırsa, sorgu doğru sonucunu vermez:
```sql
SELECT EXISTS (SELECT undefined) 
```

Alt sorgu, bir nesne içindeki seçili listedeki değerlerin listesini kapsar. Seçili listede değer yoksa, alt sorgu tek '{}' değerini döndürür. Bu değer tanımlanmıştır, bu yüzden var değeri true olarak değerlendirilir.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Örnek: Yeniden yazma ARRAY_CONTAINS ve JOIN var

Yaygın kullanım durumu ARRAY_CONTAINS, bir belgeyi dizideki bir öğenin varlığına göre filtrelemeye yönelik olur. Bu durumda, Etiketler dizisinin "turuncu" adlı bir öğe içerip içermeyeceği denetleniyoruz.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Aynı sorguyu kullanmak için tekrar yazabilirsiniz:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Ayrıca, ARRAY_CONTAINS yalnızca bir değerin dizideki herhangi bir öğeye eşit olup olmadığını kontrol edebilir. Dizi özelliklerinde daha karmaşık filtrelere ihtiyacınız varsa JOIN ' i kullanın.

Dizideki birimlere ve `nutritionValue` özelliklere göre filtreleyen aşağıdaki sorguyu göz önünde bulundurun: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Koleksiyondaki her belge için, dizi öğeleriyle bir çapraz ürün gerçekleştirilir. Bu JOIN işlemi, dizi içindeki özelliklerde filtre uygulamayı mümkün kılar. Ancak, bu sorgunun RU tüketimi önemli olacaktır. Örneğin, 1.000 belge her dizide 100 öğe içeriyorsa, bu değer 1.000 x 100 (yani, 100.000) tanımlama gruplarına genişletilir.

Mevcut, bu pahalı olmayan ürünlerden kaçınmaya yardımcı olabilir:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Bu durumda, var olan alt sorgu içindeki dizi öğelerine filtre uygulayın. Bir dizi öğesi filtreyle eşleşiyorsa, bunu projeniz ve varsa true olarak değerlendirilir.

Ayrıca diğer ad var olabilir ve projeksiyde buna başvurabilirsiniz:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Sorgu çıkışı:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>DIZI ifadesi

Bir sorgunun sonuçlarını dizi olarak proje için dızı ifadesini kullanabilirsiniz. Bu ifadeyi yalnızca sorgunun SELECT yan tümcesi içinde kullanabilirsiniz.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Sorgu çıkışı:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Diğer alt sorgularda olduğu gibi, dızı ifadesiyle filtreler de mümkündür.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Sorgu çıkışı:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Dizi ifadeleri, alt sorgularda FROM yan tümcesinden sonra da gelebilir.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Sorgu çıkışı:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB .NET örnekleri](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Belge verilerini modelleme](modeling-data.md)
