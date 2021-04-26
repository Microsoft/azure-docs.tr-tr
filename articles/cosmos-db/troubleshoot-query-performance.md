---
title: Azure Cosmos DB kullanırken karşılaşılan sorgu sorunlarını giderme
description: Azure Cosmos DB SQL sorgu sorunlarını belirlemeyi, tanılamayı ve sorun gidermeyi öğrenin.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6701a580cbe7790dcce2cbbcc46889f9dff00107
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559983"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Azure Cosmos DB kullanırken karşılaşılan sorgu sorunlarını giderme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bu makalede, Azure Cosmos DB sorgularda sorun giderme için önerilen genel bir yaklaşım gösterilmektedir. Bu makalede özetlenen adımları olası sorgu sorunlarına karşı kapsamlı bir savunma olarak düşünmemeniz durumunda, en sık karşılaşılan performans ipuçlarını buradan sunuyoruz. Azure Cosmos DB Core (SQL) API'sinde yavaş veya pahalı sorgularla ilgili sorunları gidermek için başlangıç noktası olarak bu makaleyi kullanmalısınız. Ayrıca yavaş çalışan veya önemli miktarda aktarım hızı kullanan sorguları belirlemek için [tanılama günlüklerini](cosmosdb-monitor-resource-logs.md) de kullanabilirsiniz. MongoDB için Azure Cosmos DB API kullanıyorsanız, [MongoDB sorgu sorun giderme kılavuzu için Azure Cosmos DB API 'sini](mongodb-troubleshoot-query.md) kullanmanız gerekir

Azure Cosmos DB sorgu iyileştirmeleri, genel olarak aşağıdaki gibi kategorilere ayrılmıştır:

- Sorgunun Istek birimi (RU) ücretlendirmesini azaltan iyileştirmeler
- Gecikme süresini azaltan iyileştirmeler

Bir sorgunun RU ücreti düşürüyoruz, genellikle gecikmeyi de azaltabilirsiniz.

Bu makalede, [beslenme veri kümesini](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)kullanarak yeniden oluşturabileceğiniz örnekler sağlanmaktadır.

## <a name="common-sdk-issues"></a>Yaygın SDK sorunları

Bu kılavuzu okumadan önce, sorgu altyapısıyla ilgili olmayan yaygın SDK sorunlarını göz önünde bulundurmanız yararlı olur.

- Bu [SDK performans ipuçlarını](performance-tips.md)izleyin.
    - [.NET SDK sorun giderme kılavuzu](troubleshoot-dot-net-sdk.md)
    - [Java SDK sorunlarını giderme kılavuzu](troubleshoot-java-sdk-v4-sql.md)
- SDK, sorgunuz için bir ayar yapılmasına izin verir, `MaxItemCount` ancak en az öğe sayısını belirtemezsiniz.
    - Kod, sıfırdan olan herhangi bir sayfa boyutunu işlemelidir `MaxItemCount` .
- Bazen, gelecekteki bir sayfada sonuçlar olduğunda bile sorgularda boş sayfalar bulunabilir. Bunun nedenleri şunlar olabilir:
    - SDK birden çok ağ çağrısı yapıyor olabilir.
    - Sorgunun belgeleri alması uzun sürüyor olabilir.
- Tüm sorguların, sorgunun devam etmesine izin veren bir devamlılık belirteci vardır. Sorguyu tamamen boşalttığınızdan emin olun. [Birçok sonuç sayfasını işleme](sql-query-pagination.md#handling-multiple-pages-of-results) hakkında daha fazla bilgi edinin

## <a name="get-query-metrics"></a>Sorgu ölçümlerini al

Azure Cosmos DB bir sorguyu en iyileştirirken, ilk adım her zaman sorgunuzun [sorgu ölçümlerini almak](profile-sql-api-query.md) için kullanılır. Bu ölçümler Azure portal aracılığıyla da kullanılabilir. Veri Gezgini sorgunuzu çalıştırdığınızda, sorgu ölçümleri **sonuçlar** sekmesinin yanında görünür:

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Sorgu ölçümleri alma" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Sorgu ölçümlerini aldıktan sonra, sorgularınızın **çıktı belge sayısıyla** **alınan belge sayısını** karşılaştırın. Bu makaleyi gözden geçirmek üzere ilgili bölümleri belirlemek için bu karşılaştırmayı kullanın.

**Alınan belge sayısı** , sorgu altyapısının yüklemek için gereken belge sayısıdır. **Çıktı belgesi sayısı** , sorgunun sonuçları için gereken belge sayısıdır. **Alınan belge sayısı** önemli ölçüde **çıkış belgesi** sayısından fazlaysa, sorgunuzun bir dizini kullanmayan ve tarama yapmak için gerekli olan en az bir bölümü vardı.

Senaryonuza yönelik ilgili sorgu iyileştirmelerini anlamak için aşağıdaki bölümlere bakın.

### <a name="querys-ru-charge-is-too-high"></a>Sorgunun RU ücreti çok yüksek

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Alınan belge sayısı, çıktı belge sayısından önemli ölçüde daha yüksek

- [Gerekli yolları dizin oluşturma ilkesine ekleyin.](#include-necessary-paths-in-the-indexing-policy)

- [Hangi sistem işlevlerinin dizinini kullandığını anlayın.](#understand-which-system-functions-use-the-index)

- [Dize sistemi işlev yürütmeyi geliştirir.](#improve-string-system-function-execution)

- [Hangi toplu sorguların Dizin kullandığını anlayın.](#understand-which-aggregate-queries-use-the-index)

- [Hem filtreye hem de ORDER BY yan tümcesine sahip olan sorguları iyileştirin.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Bir alt sorgu kullanarak JOIN ifadelerini iyileştirin.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Alınan belge sayısı, çıktı belge sayısına yaklaşık olarak eşit

- [Çapraz bölüm sorgularını en aza indirin.](#minimize-cross-partition-queries)

- [Birden çok özelliklerde filtre bulunan sorguları iyileştirin.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Hem filtreye hem de ORDER BY yan tümcesine sahip olan sorguları iyileştirin.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>Sorgunun RU ücreti kabul edilebilir ancak gecikme hala çok yüksek

- [Yakınlığı geliştirir.](#improve-proximity)

- [Sağlanan aktarım hızını artırın.](#increase-provisioned-throughput)

- [MaxConcurrency 'yi arttırın.](#increase-maxconcurrency)

- [MaxBufferedItemCount değerini artırın.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Alınan belge sayısı, çıkış belgesi sayısını aşarsa sorgular

 **Alınan belge sayısı** , sorgu altyapısının yüklemek için gereken belge sayısıdır. **Çıktı belgesi sayısı** , sorgu tarafından döndürülen belge sayısıdır. **Alınan belge sayısı** önemli ölçüde **çıkış belgesi** sayısından fazlaysa, sorgunuzun bir dizini kullanmayan ve tarama yapmak için gerekli olan en az bir bölümü vardı.

Aşağıda, dizin tarafından tamamen hizmet edilmemiş tarama sorgusunun bir örneği verilmiştir:

Sorgu:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Sorgu ölçümleri:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

**Alınan belge sayısı** (60.951), bu sorgunun bir belge taramasıyla sonuçlandığı **çıkış belgesi** sayısından (7) önemli ölçüde daha yüksek. Bu durumda, [Upper ()](sql-query-upper.md) sistem işlevi bir dizin kullanmaz.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Gerekli yolları dizin oluşturma ilkesine dahil et

Dizin oluşturma ilkeniz, `WHERE` yan tümceler, `ORDER BY` yan tümceler `JOIN` ve çoğu sistem işlevlerine dahil olan özellikleri kapsamalıdır. Dizin ilkesinde belirtilen yollar JSON belgelerindeki özelliklerle eşleşmelidir.

> [!NOTE]
> Azure Cosmos DB Dizin oluşturma ilkesindeki özellikler büyük/küçük harfe duyarlıdır

Aşağıdaki basit sorguyu, [beslenme](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) veri kümesinde çalıştırırsanız, `WHERE` yan tümcesindeki özelliği dizine eklendiğinde çok daha düşük bir ru ücreti gözlemleyeceksiniz:

#### <a name="original"></a>Özgün

Sorgu:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

Dizin oluşturma ilkesi:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Ru ücreti:** 409,51 Rus

#### <a name="optimized"></a>İyileştirilmiş

Dizin oluşturma ilkesi güncelleştirildi:

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

**Ru ücreti:** 2,98 Rus

Herhangi bir zamanda, yazma veya okuma kullanılabilirliği üzerinde hiçbir etkisi olmadan dizin oluşturma ilkesine özellikler ekleyebilirsiniz. [Dizin dönüştürme ilerlemesini izleyebilirsiniz](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Hangi sistem işlevlerinin Dizin kullandığını anlayın

Çoğu sistem işlevi dizinleri kullanır. Dizinler kullanan bazı yaygın dize işlevlerinin listesi aşağıda verilmiştir:

- StartsWith
- Contains
- RegexMatch
- Sol
- Alt dize-ancak yalnızca ilk num_expr 0 ise

Aşağıda, dizini kullanmayan ve bir yan tümcesinde kullanıldığında her belgeyi yüklemesi gereken bazı yaygın sistem işlevleri verilmiştir `WHERE` :

| **Sistem işlevi**                     | **İyileştirme için fikirler**             |
| --------------------------------------- |------------------------------------------------------------ |
| Üst/alt                         | Karşılaştırma sırasında verileri normalleştirmek için sistem işlevini kullanmak yerine, ekleme sırasında büyük/küçük harfleri normalleştirin. Gibi bir sorgu ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` olur ```SELECT * FROM c WHERE c.name = 'BOB'``` . |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Sorgu yürütmeden önce geçerli zamanı hesaplayın ve yan tümcesinde bu dize değerini kullanın `WHERE` . |
| Matematik işlevleri (toplamasız olmayan) | Sorgunuzda sık bir değeri hesaplamanız gerekiyorsa, değeri JSON belgenizde bir özellik olarak depolamayı düşünün. |

Bu sistem işlevleri, toplamalar ile sorgularda kullanılması dışında dizinleri kullanabilir:

| **Sistem işlevi**                     | **İyileştirme için fikirler**             |
| --------------------------------------- |------------------------------------------------------------ |
| Uzamsal sistem işlevleri                        | Sorgu sonucunu gerçek zamanlı gerçekleştirilmiş bir görünümde depolayın |

`SELECT`Yan tümcesinde kullanıldığında, verimsiz sistem işlevleri sorguların dizinleri nasıl kullanabileceğinizi etkilemez.

### <a name="improve-string-system-function-execution"></a>Dize sistemi işlev yürütmeyi geliştirme

Dizinler kullanan bazı sistem işlevleri için sorguya bir yan tümce ekleyerek sorgu yürütmeyi geliştirebilirsiniz `ORDER BY` . 

Daha belirgin bir şekilde, özelliğin önem düzeyi arttıkça,, özelliğin önemliliği arttığında, sorgudaki herhangi bir sistem işlevi, sorguda olmasının avantajını artırabilir `ORDER BY` . Bu sorgular bir dizin taraması yapar, bu nedenle sorgu sonuçlarının sıralanmasını sağlamak sorguyu daha verimli hale getirir.

Bu iyileştirme, aşağıdaki sistem işlevleri için yürütmeyi iyileştirebilirler:

- StartsWith (büyük/küçük harf duyarsız = doğru)
- Strıngequals (büyük/küçük harf duyarsız = doğru)
- Contains
- RegexMatch
- EndsWith

Örneğin, aşağıdaki sorguyu ile değerlendirin `CONTAINS` . `CONTAINS` dizinleri kullanır, ancak bazen ilgili dizin eklendikten sonra bile aşağıdaki sorguyu çalıştırırken çok yüksek bir RU ücretine de devam edebilirsiniz.

Özgün sorgu:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

Sorgu yürütmeyi şunları ekleyerek geliştirebilirsiniz `ORDER BY` :

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

Aynı iyileştirme, ek filtrelerle sorgularda yardımcı olabilir. Bu durumda, yan tümcesine eşitlik filtreleriyle özellikler de eklemek en iyisidir `ORDER BY` .

Özgün sorgu:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

`ORDER BY`(C.Name, c. Town) için [bir bileşik dizin](index-policy.md#composite-indexes) ekleyerek sorgu yürütmeyi geliştirebilirsiniz:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Hangi toplu sorguların Dizin kullandığını anlayın

Çoğu durumda, Azure Cosmos DB içindeki toplu sistem işlevleri dizini kullanır. Ancak, bir toplama sorgusunda filtrelere veya ek yan tümceciklerine bağlı olarak, çok sayıda belgeyi yüklemek için sorgu altyapısının kullanılması gerekebilir. Genellikle, sorgu altyapısı ilk olarak eşitlik ve Aralık filtrelerini uygular. Bu filtreleri uyguladıktan sonra sorgu altyapısı, ek filtreleri değerlendirebilir ve gerekirse toplamı hesaplamak için kalan belgeleri yüklemeyi çare olarak gerçekleştirebilir.

Örneğin, bu iki örnek sorgu verildiğinde, bir eşitlik ve `CONTAINS` sistem işlevi filtresiyle sorgu genellikle yalnızca bir sistem işlevi filtresiyle bir sorgudan daha verimli olacaktır `CONTAINS` . Bunun nedeni, eşitlik filtresinin önce uygulandığının ve daha pahalı bir filtre için belgelerin yüklenmesi için önce dizinin kullanıldığı bir işlemdir `CONTAINS` .

Yalnızca `CONTAINS` filtre-daha yüksek ru ücreti olan sorgu:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Hem eşitlik filtresiyle hem de `CONTAINS` filtre-alt ru ücretine sahip sorgu:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Dizini tam olarak kullanmayacak olan toplama sorgularının Ek örnekleri aşağıda verilmiştir:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Dizini kullanmayan sistem işlevleriyle sorgular

Dizini kullanıp kullanmadığını görmek için ilgili [sistem işlevinin sayfasına](sql-query-system-functions.md) başvurmalısınız.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Kullanıcı tanımlı işlevlerle (UDF) sorguları toplama

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Gruplandırma ölçütü olan sorgular

`GROUP BY`Yan tümcesindeki özelliklerin kardinalitesi arttıkça, ile SORGULARıN ru ücreti artar `GROUP BY` . Aşağıdaki sorguda, örneğin, benzersiz açıklamaların arttığı sayı olarak sorgunun RU ücreti artar.

Yan tümce içeren bir toplama işlevinin RU ücreti, `GROUP BY` tek başına bir toplama IŞLEVININ ru ücretinden daha yüksek olacaktır. Bu örnekte, sorgu altyapısının, `c.foodGroup = "Sausages and Luncheon Meats"` ru ücretinden yüksek olması beklendiğinden filtreyle eşleşen her belgeyi yüklemesi gerekir.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Aynı toplama sorgularını sıklıkla çalıştırmayı planlıyorsanız, tek tek sorguların çalıştırılmasından [Azure Cosmos DB değişiklik akışı](change-feed.md) ile gerçek zamanlı gerçekleştirilmiş bir görünüm oluşturmak daha verimli olabilir.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Hem filtreye hem de ORDER BY yan tümcesine sahip sorguları iyileştirin

Bir filtreye ve `ORDER BY` yan tümcesine sahip sorgular normalde bir Aralık dizini kullanır, ancak bir bileşik dizinden sunulabilen daha verimli olacaktır. Dizin oluşturma ilkesini değiştirmenin yanı sıra, bileşik dizindeki tüm özellikleri `ORDER BY` yan tümcesine eklemeniz gerekir. Sorguya yapılan bu değişiklik, bileşik dizini kullandığından emin olur.  Bir sorgu [çalıştırarak, bu](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) etkiyi gözlemleyebilirsiniz:

#### <a name="original"></a>Özgün

Sorgu:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

Dizin oluşturma ilkesi:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Ru ücreti:** 44,28 Rus

#### <a name="optimized"></a>İyileştirilmiş

Sorgu güncelleştirildi (yan tümcesindeki her iki özelliği de içerir `ORDER BY` ):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Dizin oluşturma ilkesi güncelleştirildi:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Ru ücreti:** 8,86 Rus

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Alt sorgu kullanarak JOIN ifadelerini iyileştirme

Çoklu değer alt sorguları `JOIN` , yan Tümcecikteki tüm çapraz birleşimler yerine her bir SELECT-many ifadesinden sonra koşulları ileterek ifadeleri en iyileştirebilir `WHERE` .

Şu sorguyu göz önünde bulundurun:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Ru ücreti:** 167,62 Rus

Bu sorgu için dizin adı `infant formula` , `nutritionValue` 0 ' dan büyük ve 1 ' den büyük bir etiketi olan herhangi bir belgeyle eşleştirecektir `amount` . `JOIN`Buradaki ifade, herhangi bir filtre uygulanmadan önce, eşleşen her belge için etiketlerin, nuttastaların ve servilerlerdeki tüm öğelerin çapraz çarpımını gerçekleştirecek. `WHERE`Yan tümce daha sonra her bir tanımlama grubu için filtre koşulunu uygular `<c, t, n, s>` .

Örneğin, eşleşen bir belge üç dizide her birinde 10 öğe içeriyorsa, 1 x 10 x 10 x 10 (diğer bir deyişle, 1.000) tanımlama gruplarına genişletilir. Burada alt sorgular kullanılması, birleştirilmiş dizi öğelerinin sonraki ifadeyle katılmadan önce filtrelemeye yardımcı olabilir.

Bu sorgu, önceki bir ile eşdeğerdir, ancak alt sorgular kullanır:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru ücreti:** 22,17 Rus

Etiketler dizisindeki yalnızca bir öğenin filtreyle eşleştiğini ve hem nutriler hem de Server dizileri için beş öğe olduğunu varsayalım. `JOIN`İfadeler, ilk sorgudaki 1.000 öğeden farklı olarak 1 x 1 x 5 x 5 = 25 öğe olacak şekilde genişletilir.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Alınan belge sayısı, çıkış belgesi sayısına eşit olan sorgular

**Alınan belge sayısı** yaklaşık olarak **çıktı belge sayısına** eşitse, sorgu altyapısının çok sayıda gereksiz belgeyi taraması gerekmez. Anahtar sözcüğünü kullananlar gibi birçok sorgu için `TOP` , **alınan belge sayısı** , **çıkış belgesi sayısını** 1 ' den fazla olabilir. Bunun için endişelenmeniz gerekmez.

### <a name="minimize-cross-partition-queries"></a>Çapraz bölüm sorgularını en aza indir

Azure Cosmos DB, Istek birimi ve veri depolama alanı artışına göre bağımsız kapsayıcıları ölçeklendirmek için [bölümleme](partitioning-overview.md) kullanır. Her fiziksel bölümün ayrı ve bağımsız bir dizini vardır. Sorgunuzun, kapsayıcının bölüm anahtarı ile eşleşen bir eşitlik filtresi varsa, yalnızca ilgili bölümün dizinini denetlemeniz gerekir. Bu iyileştirme, sorgunun gerektirdiği toplam ru sayısını azaltır.

Çok sayıda sağlanan ru (30.000 ' den fazla) veya büyük miktarda veri depolanmış (yaklaşık 100 GB 'tan fazla) varsa, sorgu RU ücretlerinde önemli bir düşüş görmeniz için büyük olasılıkla çok sayıda kapsayıcısına sahip olursunuz.

Örneğin, bölüm anahtarı, \ Grup adlı bir kapsayıcı oluşturursanız, aşağıdaki sorguların yalnızca tek bir fiziksel bölümü denetlemesi gerekir:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

`IN`Bölüm anahtarı ile bir filtresi olan sorgular yalnızca ilgili fiziksel bölümleri denetleyecek ve "fan" olarak olmayacaktır:

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Bölüm anahtarında Aralık filtreleri olan veya bölüm anahtarında filtre bulunmayan sorgular, "fan" ve sonuçlar için her fiziksel bölümün dizinini denetleyecek.

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Birden çok özelliklerde filtre bulunan sorguları iyileştirin

Birden çok özellik üzerinde filtre bulunan sorgular normalde bir Aralık dizini kullanır, ancak bir bileşik dizinden sunulabilen daha verimli olur. Küçük miktarlarda veri için bu iyileştirme önemli bir etkiye sahip olmayacaktır. Ancak, büyük miktarlarda veri için yararlı olabilir. Bileşik dizin başına yalnızca en çok bir eşitlik olmayan filtreyi en iyi hale getirebilirsiniz. Sorgunuzun birden çok eşitlik olmayan filtresi varsa, bileşik dizini kullanacak şekilde bunlardan birini seçin. Rest, Aralık dizinlerini kullanmaya devam edecektir. Eşitlik olmayan filtrenin en son bileşik dizinde tanımlanması gerekir. [Bileşik dizinler hakkında daha fazla bilgi edinin](index-policy.md#composite-indexes).

Bileşik bir dizin ile iyileştirilen bazı sorgu örnekleri aşağıda verilmiştir:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

İlgili bileşik dizin aşağıda verilmiştir:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Sorgu gecikmesini azaltan iyileştirmeler

Birçok durumda, sorgu gecikmesi hala çok yüksek olduğunda RU ücreti kabul edilebilir durumda olabilir. Aşağıdaki bölümler sorgu gecikmesini azaltmaya yönelik ipuçlarına genel bakış sunar. Aynı sorguyu aynı veri kümesinde birden çok kez çalıştırırsanız, genellikle her seferinde aynı RU ücretine sahip olur. Ancak sorgu gecikmesi sorgu yürütmeleri arasında farklılık gösterebilir.

### <a name="improve-proximity"></a>Yakınlığı geliştirme

Azure Cosmos DB hesabından farklı bir bölgeden çalıştırılan sorgular, aynı bölgede çalıştırıldıklarından daha yüksek gecikme süresine sahip olur. Örneğin, masaüstü bilgisayarınızda kod çalıştırıyorsanız, sorgunun aynı Azure bölgesindeki bir sanal makineden Azure Cosmos DB kadar (veya daha fazla) olması beklendiğinden, gecikme süresinin on veya yüzlerce milisaniyelik (veya daha fazla) olması beklenir. Verilerinizi uygulamanıza yakın bir şekilde getirebilmeniz için [Azure Cosmos DB verileri genel olarak dağıtmak](distribute-data-globally.md) basit bir işlemdir.

### <a name="increase-provisioned-throughput"></a>Sağlanan aktarım hızını artır

Azure Cosmos DB, sağlanan aktarım hızı, Istek birimleri (ru) cinsinden ölçülür. 5 ' lik bir iş hacmi tüketen bir sorgunuz olduğunu düşünün. Örneğin, 1.000 ru 'yi sağlarsanız, bu sorguyu saniye başına 200 kez çalıştırabileceksiniz. Kullanılabilir yeterli üretilen iş olmadığında sorguyu çalıştırmayı denediyseniz Azure Cosmos DB bir HTTP 429 hatası döndürür. Geçerli çekirdek (SQL) API SDK 'Ları, kısa bir süre bekledikten sonra bu sorguyu otomatik olarak yeniden dener. Kısıtlanmış istekler daha uzun sürer, bu nedenle sağlanan verimlilik artarak sorgu gecikmesi iyileştirebilirler. Azure portal **ölçümler** dikey penceresinde [Toplam kısıtlanmış istek sayısını](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) gözlemleyebilirsiniz.

### <a name="increase-maxconcurrency"></a>MaxConcurrency 'yi artır

Paralel sorgular birden çok bölümü paralel olarak sorgulayarak çalışır. Ancak tek bölümlü bir koleksiyondaki veriler sorguya göre seri olarak getirilir. Bu nedenle, MaxConcurrency 'yi bölüm sayısına ayarlarsanız en iyi performansı elde etmeniz, diğer tüm sistem koşullarının aynı kalmasını sağlamak için en iyi performansa sahip olursunuz. Bölüm sayısını bilmiyorsanız, Maxeşzamanlılık (veya daha eski SDK sürümlerindeki Maxdegreesofparalellik) düzeyini yüksek bir sayı olarak ayarlayabilirsiniz. Sistem maksimum paralellik derecesi olarak en az (bölüm sayısı, Kullanıcı tarafından girilen giriş) seçer.

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount değerini artır

Sorgular, geçerli sonuç toplu işi istemci tarafından işlendiği sırada sonuçları önceden getirmek üzere tasarlanmıştır. Önceden getirme, bir sorgunun genel gecikme süresini artırmaya yardımcı olur. MaxBufferedItemCount ayarı, önceden getirilen sonuçların sayısını sınırlar. Bu değeri, beklenen sonuç sayısı (veya daha yüksek bir sayı) olarak ayarlarsanız, sorgu ön alma işleminden en avantaja sahip olabilir. Bu değeri-1 olarak ayarlarsanız, sistem arabelleğe eklenecek öğe sayısını otomatik olarak belirleyecek.

## <a name="next-steps"></a>Sonraki adımlar
Her sorgu için ru ölçüyle ilgili bilgiler için aşağıdaki makalelere bakın, sorgularınızı ayarlamak için yürütme istatistiklerini alın ve daha fazlasını yapın:

* [.NET SDK kullanarak SQL sorgu yürütme ölçümleri alın](profile-sql-api-query.md)
* [Azure Cosmos DB ile sorgu performansını ayarlama](./sql-api-query-metrics.md)
* [.NET SDK’sı için performans ipuçları](performance-tips.md)
* [Java v4 SDK için performans ipuçları](performance-tips-java-sdk-v4-sql.md)