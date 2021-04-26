---
title: Azure Cosmos DB Dizin oluşturma ilkeleri
description: Azure Cosmos DB ' de otomatik dizin oluşturma ve daha fazla performans için varsayılan dizin oluşturma ilkesini yapılandırma ve değiştirme hakkında bilgi edinin.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: tisande
ms.openlocfilehash: 26465eb9826c60daad7b44e1c2fe6ae3c19b1ed0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378817"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB'de dizin oluşturma ilkeleri
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB'de her kapsayıcının bir dizin oluşturma ilkesi vardır ve bu ilke kapsayıcıdaki öğelerin nasıl dizine alınacağını belirler. Yeni oluşturulan kapsayıcıların varsayılan dizin oluşturma ilkesi tüm öğelerin tüm özelliklerini dizine alır ve tüm dizeler veya sayılar için aralık dizinlerini zorunlu tutar. Bu, dizin oluşturma ve dizin yönetimi ile ilgili düşünmek zorunda kalmadan iyi bir sorgu performansı almanızı sağlar.

Bazı durumlarda bu otomatik davranışı kendi gereksinimlerinize daha iyi uyacak şekilde geçersiz kılmak isteyebilirsiniz. *Dizin oluşturma modunu* ayarlayarak bir kapsayıcının dizin oluşturma ilkesini özelleştirebilir ve *özellik yollarını* dahil edebilir veya dışlayabilirsiniz.

> [!NOTE]
> Bu makalede açıklanan dizin oluşturma ilkelerini güncelleştirme yöntemi yalnızca Azure Cosmos DB SQL (Core) API 'SI için geçerlidir. [MongoDB için Azure Cosmos DB API](mongodb-indexing.md) 'sinde dizin oluşturma hakkında bilgi edinin

## <a name="indexing-mode"></a>Dizin oluşturma modu

Azure Cosmos DB iki dizin oluşturma modunu destekler:

- **Tutarlı**: öğe oluşturma, güncelleştirme veya silme işlemi sırasında dizin zaman uyumlu olarak güncelleştirilir. Bu, okuma sorgularınızın tutarlılığı, [hesap için yapılandırılmış tutarlılığa](consistency-levels.md)sahip olacağı anlamına gelir.
- **Hiçbiri**: Dizin oluşturma kapsayıcıda devre dışı bırakıldı. Bu genellikle bir kapsayıcı, ikincil dizinlere gerek olmadan saf anahtar-değer deposu olarak kullanıldığında kullanılır. Toplu işlemlerin performansını artırmak için de kullanılabilir. Toplu işlemler tamamlandıktan sonra, dizin modu tutarlı olarak ayarlanabilir ve sonra, Işlem tamamlanana kadar [ındexdönüşümle ilerlemesi](how-to-manage-indexing-policy.md#dotnet-sdk) kullanılarak izlenebilir.

> [!NOTE]
> Azure Cosmos DB, yavaş dizin oluşturma modunu da destekler. Gecikmeli dizinde, dizin güncelleştirmeleri altyapıda hiçbir çalışma yapılmadığı sırada çok daha düşük öncelik düzeyinde gerçekleştirilir. Bu **tutarsız veya eksik** sorgu sonuçlarına yol açabilir. Cosmos kapsayıcısını sorgulamayı planlıyorsanız gecikmeli dizini seçmemelisiniz. Yeni kapsayıcılar geç dizin oluşturmayı seçemezsiniz. [Azure desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ile iletişim kurarak (yavaş Dizin oluşturmayı desteklemeyen [sunucusuz](serverless.md) modda bir Azure Cosmos hesabı kullanıyor olmanız dışında) bir istisna isteyebilirsiniz.

Varsayılan olarak, dizin oluşturma ilkesi olarak ayarlanır `automatic` . `automatic`Dizin oluşturma ilkesindeki özelliği olarak ayarlanarak elde edilir `true` . Bu özelliği ayarlamak için `true` Azure CosmosDB 'nin belgeleri yazıldığı gibi otomatik olarak dizin oluşturulmasına izin verir.

## <a name="index-size"></a><a id="index-size"></a>Dizin boyutu

Azure Cosmos DB, tüketilen toplam depolama alanı, hem veri boyutu hem de dizin boyutunun birleşimidir. Aşağıda Dizin boyutunun bazı özellikleri verilmiştir:

* Dizin boyutu dizin oluşturma ilkesine bağlıdır. Tüm özellikler dizine alınmışsa, dizin boyutu veri boyutundan daha büyük olabilir.
* Veriler silindiğinde, dizinler neredeyse sürekli olarak sıkıştırılır. Ancak, küçük veri silme işlemleri için Dizin boyutundaki azalmayı hemen gözlemleyebilirsiniz.
* Fiziksel bölümler bölündüğünde Dizin boyutu geçici olarak genişleyebilir. Bölüm bölme işlemi tamamlandıktan sonra Dizin alanı serbest bırakılır.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Özellik yollarını dahil etme ve hariç tutma

Özel bir dizin oluşturma ilkesi, dizin oluşturma işleminden açıkça dahil edilen veya dışlanan Özellik yollarını belirtebilir. Dizine alınmış yolların sayısını en iyi duruma getirerek, yazma işlemlerinin gecikme süresini ve RU ücretlendirmesini önemli ölçüde azaltabilirsiniz. Bu yollar, [Dizin oluşturma genel bakış bölümünde açıklanan yöntemi](index-overview.md#from-trees-to-property-paths) aşağıdaki eklemelerle izleyerek tanımlanmıştır:

- skaler bir değere (dize veya sayı) öndeki bir yol, şununla biter `/?`
- bir dizideki öğeler `/[]` , Gösterim (yerine `/0` , `/1` vb.) ile birlikte karşılanır
- `/*`joker karakter, düğümün altındaki herhangi bir öğeyi eşleştirmek için kullanılabilir

Aynı örneği yeniden almak:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `headquarters` `employees` yolu`/headquarters/employees/?`

- `locations`' `country` yol`/locations/[]/country/?`

- altında herhangi bir şeyin yolu `headquarters``/headquarters/*`

Örneğin, yolunu dahil eteceğiz `/headquarters/employees/?` . Bu yol çalışanlar özelliğini dizinliyoruz, ancak bu özellik içinde iç içe geçmiş JSON dizinini dizinliyoruz.

## <a name="includeexclude-strategy"></a>Dahil etme/hariç tutma stratejisi

Herhangi bir dizin oluşturma ilkesinin kök yolu `/*` dahil edilen ya da hariç tutulmuş bir yol olarak içermesi gerekmez.

- Dizin oluşturma gerektirmeyen yolları seçmeli olarak hariç tutmak için kök yolu ekleyin. Bu, modelinize eklenebilen yeni bir özelliğin Azure Cosmos DB proaktif olarak dizinlemenizi sağlayan bu, önerilen yaklaşımdır.
- Dizine eklenmesi gereken yolları seçmeli olarak dahil etmek için kök yolu hariç tutun.

- : Alfasayısal karakterler ve _ (alt çizgi) içeren normal karakter içeren yollar için, yol dizesinin çift tırnak etrafında (örneğin, "/path/?") kaçış olması gerekmez. Diğer özel karakterlere sahip yollar için, yol dizesini çift tırnak etrafında (örneğin, "/ \" Path-ABC \" /?") kaçış yapmanız gerekir. Yolunuzda özel karakterler bekleliyorsanız, güvenlik için her yolu da kaçış yapabilirsiniz. İşlevsellik, tüm yolları yalnızca özel karakterlere sahip olanlara karşı atladıysanız herhangi bir farklılık yapmaz.

- `_etag`ETag, dizin oluşturma için eklenen yola eklenmediği takdirde, varsayılan olarak dizin oluşturma işleminden çıkarılır.

- Dizin oluşturma modu **tutarlı** olarak ayarlandıysa, sistem özellikleri `id` ve `_ts` otomatik olarak dizinlenir.

Yolları dahil etme ve hariç tutma sırasında aşağıdaki özniteliklerle karşılaşabilirsiniz:

- `kind` ya da olabilir `range` `hash` . Karma dizin desteği eşitlik filtreleriyle sınırlıdır. Aralık dizini işlevselliği, karma dizinlerin tüm işlevlerini ve etkili sıralama, Aralık filtreleri, sistem işlevlerini sağlar. Her zaman bir Aralık dizini kullanmanızı öneririz.

- `precision` , eklenen yollar için dizin düzeyinde tanımlanmış bir sayıdır. Değeri `-1` en fazla duyarlığı gösterir. Bu değeri her zaman olarak ayarlamayı öneririz `-1` .

- `dataType` ya da olabilir `String` `Number` . Bu, dizine eklenecek JSON özelliklerinin türlerini gösterir.

Belirtilmediğinde, bu özellikler aşağıdaki varsayılan değerlere sahip olur:

| **Özellik Adı**     | **Varsayılan değer** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` ve `Number` |

Yolların dahil edilmesi ve dışlanması için ilke örneklerinin dizinini oluşturma [bölümüne](how-to-manage-indexing-policy.md#indexing-policy-examples) bakın.

## <a name="includeexclude-precedence"></a>Dahil etme/hariç tutma önceliği

Dahil edilen yollarınızın ve dışlanan yolların bir çakışması varsa, daha kesin yol daha önceliklidir.

Aşağıda bir örnek verilmiştir:

**Dahil edilen yol**: `/food/ingredients/nutrition/*`

**Dışlanan yol**: `/food/ingredients/*`

Bu durumda, dahil edilen yol daha kesin olduğundan, dışlanan yol üzerinden önceliklidir. Bu yollara bağlı olarak, `food/ingredients` yoldaki veya iç içe yerleştirilmiş tüm veriler dizinden dışlanıyor. Özel durum, dahil edilen yol içindeki veriler olabilir: `/food/ingredients/nutrition/*` , Dizin oluşturulacak.

Azure Cosmos DB, dahil edilen ve dışlanan yolların önceliği için bazı kurallar aşağıda verilmiştir:

- Daha derin yollar daha dar yollardan daha belirgin. Örneğin: `/a/b/?` daha kesin `/a/?` .

- `/?`Daha kesin `/*` . Örneğin `/a/?` , öncelik alından daha kesin bir değer `/a/*` `/a/?` alır.

- Yol, `/*` içerilen bir yol veya dışlanan yol olmalıdır.

## <a name="spatial-indexes"></a>Uzamsal dizinler

Dizin oluşturma ilkesinde bir uzamsal yol tanımladığınızda, bu yola hangi dizinin uygulanacağını tanımlamanız gerekir ```type``` . Uzamsal dizinler için olası türler şunlardır:

* Seçeneğinin

* Poligon

* MultiPolygon

* LineString

Azure Cosmos DB, varsayılan olarak hiçbir uzamsal dizin oluşturmaz. Uzamsal SQL yerleşik işlevlerini kullanmak isterseniz, gereken özelliklerde bir uzamsal dizin oluşturmanız gerekir. Uzamsal dizinler eklemeye yönelik dizin oluşturma örnekleri için [Bu bölüme](sql-query-geospatial-index.md) bakın.

## <a name="composite-indexes"></a>Bileşik dizinler

`ORDER BY`İki veya daha fazla özelliği olan bir yan tümcesine sahip sorgular bileşik bir dizin gerektirir. Ayrıca, birçok eşitlik ve Aralık sorgusunun performansını artırmak için bir bileşik dizin tanımlayabilirsiniz. Varsayılan olarak, bir bileşik dizin tanımlanmadığında, gereken şekilde [Bileşik dizinler eklemelisiniz](how-to-manage-indexing-policy.md#composite-index) .

Dahil edilen veya dışlanan yolların aksine, joker karakterle bir yol oluşturamazsınız `/*` . Her bileşik yol, `/?` belirtmeniz gerekmeyen yolun sonuna örtülü olarak sahiptir. Bileşik yollar skaler bir değere yol açabilir ve bileşik dizine dahil olan tek değerdir.

Bileşik dizin tanımlarken şunu belirtirsiniz:

- İki veya daha fazla özellik yolu. Özellik yollarının önemli olarak tanımlandığı sıra.

- Sıra (artan veya azalan).

> [!NOTE]
> Bileşik dizin eklediğinizde, yeni bileşik dizin ekleme tamamlanana kadar sorgu mevcut Aralık dizinlerini kullanır. Bu nedenle, bileşik bir dizin eklediğinizde performans iyileştirmelerini hemen gözlemleyebilirsiniz. [SDK 'lardan birini kullanarak](how-to-manage-indexing-policy.md)Dizin dönüşümünün ilerlemesini izlemek mümkündür.

### <a name="order-by-queries-on-multiple-properties"></a>Birden çok özelliklerde sorguya göre sırala:

`ORDER BY`İki veya daha fazla özelliği olan bir yan tümcesine sahip sorgular için Bileşik dizinler kullanılırken aşağıdaki noktalar kullanılır:

- Bileşik dizin yolları yan tümcesindeki özelliklerin dizisiyle eşleşmezse `ORDER BY` , bileşik dizin sorguyu desteklemiyor.

- Bileşik dizin yollarının sırası (artan veya azalan), yan tümcesindeki ile de eşleşmelidir `order` `ORDER BY` .

- Bileşik dizin aynı zamanda `ORDER BY` tüm yollarda ters sırada olan bir yan tümceyi destekler.

Bir bileşik dizinin özellikler adı, yaşı ve _ts tanımlı olduğu aşağıdaki örneği göz önünde bulundurun:

| **Bileşik dizin**     | **Örnek `ORDER BY` sorgu**      | **Bileşik dizin desteği** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Tüm gerekli sorgulara hizmeti sağlamak için dizin oluşturma ilkenizi özelleştirmeniz gerekir `ORDER BY` .

### <a name="queries-with-filters-on-multiple-properties"></a>Birden fazla özelliğinde filtreler olan sorgular

Bir sorgu iki veya daha fazla özelliğe filtre içeriyorsa, bu özellikler için bileşik bir dizin oluşturmak yararlı olabilir.

Örneğin, hem eşitlik hem de Aralık filtresi içeren aşağıdaki sorguyu göz önünde bulundurun:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18
```

Bu sorgu daha verimli olacaktır, daha az zaman alır ve üzerinde bileşik bir dizinden yararlanıyorsa daha az RU `(name ASC, age ASC)` .

Birden çok Aralık filtresi içeren sorgular, bileşik bir dizinle da iyileştirilebilir. Ancak, her bir bileşik dizin yalnızca tek bir Aralık filtresini iyileştirebilirler. Aralık filtreleri,,, `>` `<` ve içerir `<=` `>=` `!=` . Aralık filtresi, en son bileşik dizinde tanımlanmalıdır.

Aşağıdaki sorguyu bir eşitlik filtresi ve iki Aralık filtresi ile birlikte düşünün:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18 AND c._ts > 1612212188
```

Bu sorgu, ve üzerindeki bileşik bir dizinle daha etkili olacaktır `(name ASC, age ASC)` `(name ASC, _ts ASC)` . Ancak, sorgu üzerinde bir bileşik dizin kullanılmaz, `(age ASC, name ASC)` çünkü eşitlik filtreleriyle özellikler ilk olarak bileşik dizinde tanımlanmalıdır. `(name ASC, age ASC, _ts ASC)`Her bileşik dizin yalnızca tek bir Aralık filtresini iyileştirebileceğinizden, üzerinde tek bir bileşik dizin yerine iki ayrı bileşik dizin gereklidir.

Birden çok özelliklerde filtre içeren sorgular için Bileşik dizinler oluşturulurken aşağıdaki noktalar kullanılır

- Filtre ifadeleri birden çok bileşik dizin kullanabilir.
- Sorgunun filtresindeki özellikler, bileşik dizinindekilerle eşleşmelidir. Bir özellik bileşik dizindaysa, ancak sorguya filtre olarak eklenmemelidir, sorgu bileşik dizinden yararlanmaz.
- Bir sorguda, bir bileşik dizinde tanımlanmayan ek özellikler varsa, sorguyu değerlendirmek için bileşik ve Aralık dizinlerinin bir birleşimi kullanılır. Bu, Aralık dizinleri kullanılarak özel olarak daha az RU gerektirir.
- Bir özelliğin Aralık filtresi varsa (,, `>` , `<` `<=` `>=` veya `!=` ), bu özellik bileşik dizinde son olarak tanımlanmalıdır. Bir sorguda birden fazla Aralık filtresi varsa, bu birden çok bileşik dizinlerden yararlanabilir.
- Birden çok filtre içeren sorguları iyileştirmek için bir bileşik dizin oluştururken, `ORDER` bileşik dizinin sonuçları üzerinde hiçbir etkisi olmayacaktır. Bu özellik isteğe bağlıdır.

Bir bileşik dizinin özellikler adı, yaşı ve zaman damgasında tanımlandığı aşağıdaki örnekleri göz önünde bulundurun:

| **Bileşik dizin**     | **Örnek sorgu**      | **Bileşik dizin desteği** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-and-order-by"></a>Filtre ve SıRALAMA ölçütü olan sorgular

Bir sorgu bir veya daha fazla özellik üzerinde filtreleyip ORDER BY yan tümcesinde farklı özelliklere sahipse, filtrenin içindeki özellikleri yan tümcesine eklemek yararlı olabilir `ORDER BY` .

Örneğin, bir filtre içindeki özellikleri `ORDER BY` yan tümcesine ekleyerek, bir bileşik dizinden yararlanmak için aşağıdaki sorgu yeniden yazılabilir:

Aralık dizinini kullanan sorgu:

```sql
SELECT *
FROM c 
WHERE c.name = "John" 
ORDER BY c.timestamp
```

Bileşik dizin kullanarak sorgula:

```sql
SELECT * 
FROM c 
WHERE c.name = "John"
ORDER BY c.name, c.timestamp
```

Aynı sorgu iyileştirmeleri, filtre içeren herhangi bir sorgu için genelleştirilemez `ORDER BY` , tek tek bileşik dizinlerin yalnızca en çok bir Aralık filtresini destekleyebileceğini aklınızda bulundurun.

Aralık dizinini kullanan sorgu:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.timestamp
```

Bileşik dizin kullanarak sorgula:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.name, c.age, c.timestamp
```

Bunlara ek olarak, sistem işlevleriyle ve SıRASıYLA yapılan sorguları iyileştirmek için bileşik dizinleri kullanabilirsiniz:

Aralık dizinini kullanan sorgu:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.lastName
```

Bileşik dizin kullanarak sorgula:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.firstName, c.lastName
```

Bir sorguyu bir filtre ve yan tümcesiyle iyileştirmek için Bileşik dizinler oluştururken aşağıdaki noktalar geçerlidir `ORDER BY` :

* Bir özellikte filtre içeren bir sorgu üzerinde bir bileşik dizin tanımlamadıysanız ve `ORDER BY` farklı bir özellik kullanarak ayrı bir yan tümce kullanırsanız, sorgu yine de başarılı olur. Ancak, özellikle `ORDER BY` yan tümcesindeki özelliğin yüksek bir kardinalite özelliği varsa, SORGUNUN ru maliyeti bileşik bir dizinle azaltılabilir.
* Sorgu, özelliklere filtre uygular, bu, ilk olarak `ORDER BY` yan tümcesine eklenmelidir.
* Sorgu birden çok özelliğe filtre uygular, eşitlik filtreleri yan tümcesindeki ilk Özellikler olmalıdır `ORDER BY` .
* Sorgu birden çok özellik üzerinde filtreleyip, bileşik dizin başına en fazla bir Aralık filtresi veya sistem işleviniz olabilir. Aralık filtresinde veya sistem işlevinde kullanılan özellik, en son bileşik dizinde tanımlanmalıdır.
* Birden çok özelliği olan sorgular için Bileşik dizinler oluşturmaya yönelik tüm hususlar ve `ORDER BY` birden çok özelliklerde filtre içeren sorgular hala geçerlidir.


| **Bileşik dizin**                      | **Örnek `ORDER BY` sorgu**                                  | **Bileşik dizin desteği** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

### <a name="queries-with-a-filter-and-an-aggregate"></a>Filtre ve toplama içeren sorgular 

Bir sorgu bir veya daha fazla özellik üzerinde filtreleyip bir toplam sistem işlevi içeriyorsa, filtre ve toplam sistem işlevindeki özellikler için bir bileşik dizin oluşturmak yararlı olabilir. Bu iyileştirme, [Sum](sql-query-aggregate-sum.md) ve [AVG](sql-query-aggregate-avg.md) sistem işlevleri için geçerlidir.

Bir sorguyu filtre ve toplu sistem işleviyle iyileştirmek için Bileşik dizinler oluştururken aşağıdaki noktalar geçerlidir.

* Sorguları toplamalar ile çalıştırırken Bileşik dizinler isteğe bağlıdır. Ancak, sorgunun RU maliyeti genellikle bileşik bir dizinle önemli ölçüde azaltılabilir.
* Sorgu birden çok özelliğe filtre uygular, eşitlik filtreleri bileşik dizindeki ilk Özellikler olmalıdır.
* Bileşik dizin başına en fazla bir Aralık filtresine sahip olabilirsiniz ve toplam sistem işlevindeki özellikte olması gerekir.
* Toplu sistem işlevindeki özellik, en son bileşik dizin içinde tanımlanmalıdır.
* `order`( `ASC` Veya `DESC` ) bunun önemi yoktur.

| **Bileşik dizin**                      | **Örnek sorgu**                                  | **Bileşik dizin desteği** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name > "John"``` | `No` |
| ```(name ASC, age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age = 25``` | `Yes` |
| ```(age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age > 25``` | `No` |

## <a name="index-transformationmodifying-the-indexing-policy"></a>Dizin oluşturma ilkesi <Dizin dönüştürme>değiştirme

Kapsayıcının dizin oluşturma ilkesi [, Azure Portal veya desteklenen SDK 'lardan birini kullanarak](how-to-manage-indexing-policy.md)dilediğiniz zaman güncelleştirilemeyebilir. Dizin oluşturma ilkesi için bir güncelleştirme, eski dizinden yeni bir dönüştürmeyi tetikler ve bu, çevrimiçi ve yerinde gerçekleştirilir (Bu nedenle işlem sırasında ek depolama alanı tüketilmemelidir). Eski dizin oluşturma ilkesi, yazma kullanılabilirliği, okuma kullanılabilirliği veya kapsayıcıda sağlanan aktarım hızı etkilenmeden yeni ilkeye etkin bir şekilde dönüştürülür. Dizin dönüştürme zaman uyumsuz bir işlemdir ve tamamlanmak üzere gereken süre, sağlanan aktarım hızına, öğe sayısına ve bunların boyutuna bağlıdır.

> [!IMPORTANT]
> Dizin dönüştürme, [Istek birimlerini](request-units.md)tüketen bir işlemdir. Bir dizin dönüştürmesi tarafından tüketilen istek birimleri Şu anda [sunucusuz](serverless.md) kapsayıcılar kullanıyorsanız faturalandırılmaz. Bu Istek birimleri, sunucusuz genel kullanıma sunulduğunda faturalandırılır.

> [!NOTE]
> [SDK 'lardan birini kullanarak](how-to-manage-indexing-policy.md)Dizin dönüşümünün ilerlemesini izlemek mümkündür.

Herhangi bir dizin dönüştürmesi sırasında kullanılabilirliği yazmanın bir etkisi yoktur. Dizin dönüştürme, sağlanan RUs 'yi, CRUD işlemlerinizin veya sorgulardan daha düşük bir öncelikte kullanır.

Yeni Dizin eklenirken kullanılabilirliği okuma etkisi yoktur. Sorgular, Dizin dönüştürme işlemi tamamlandıktan sonra yalnızca yeni dizinleri kullanacaktır. Dizin dönüştürmesi sırasında, sorgu altyapısı var olan dizinleri kullanmaya devam eder, bu nedenle dizinleme değişikliğini başlatmadan önce gözlemlediğiniz şekilde, dizin oluşturma dönüştürmesi sırasında benzer okuma performansını gözlemleyeceksiniz. Yeni dizinler eklenirken tamamlanmamış veya tutarsız sorgu sonuçlarının de riski yoktur.

Dizinler kaldırılırken ve bırakılan dizinlerde filtre uygulayan sorguları hemen çalıştırdığınızda, tutarlı veya tamamlanmış sorgu sonuçlarının garantisi yoktur. Birden çok dizini kaldırır ve tek bir dizin oluşturma İlkesi değişikliğini yaparsanız sorgu altyapısı, Dizin dönüştürmesi boyunca tutarlı ve tamamlanmış sonuçlar sağlar. Ancak, birden çok dizin oluşturma ilkesi değişikliği aracılığıyla dizinleri kaldırırsanız, tüm dizin dönüştürmeleri tamamlanana kadar sorgu altyapısı tutarlı veya tamamlanmamış sonuçlar vermez. Çoğu geliştirici dizinleri çalıştırmaz ve bu dizinleri kullanan sorguları hemen çalıştırmayı dener. bu durum, uygulamada düşüktür.

> [!NOTE]
> Mümkün olduğunda, her zaman birden çok dizin oluşturma değişikliğini tek bir dizin oluşturma ilkesi değişikliğine göre gruplemeye çalışırsınız

## <a name="indexing-policies-and-ttl"></a>Dizin oluşturma ilkeleri ve TTL

[Yaşam süresi (TTL) özelliğinin kullanılması için](time-to-live.md) dizin oluşturma gerekir. Bunun anlamı:

- Dizin oluşturma modunun olarak ayarlandığı bir kapsayıcıda TTL 'yi etkinleştirmek mümkün değildir `none` ,
- TTL 'nin etkinleştirildiği bir kapsayıcıda dizin oluşturma modunun hiçbiri olarak ayarlanması mümkün değildir.

Hiçbir özellik yolunun dizine alınması gereken ancak TTL 'nin gerekli olduğu senaryolarda, dizin oluşturma modu olarak ayarlanmış bir dizin oluşturma ilkesi kullanabilirsiniz, `consistent` dahil edilen yol yok, ve `/*` tek Dışlanan yol

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde dizin oluşturma hakkında daha fazla bilgi edinin:

- [Dizinlemeye genel bakış](index-overview.md)
- [Dizin oluşturma ilkesini yönetme](how-to-manage-indexing-policy.md)
