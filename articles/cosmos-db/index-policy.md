---
title: Azure Cosmos DB Dizin oluşturma ilkeleri
description: Azure Cosmos DB ' de otomatik dizin oluşturma ve daha fazla performans için varsayılan dizin oluşturma ilkesini yapılandırma ve değiştirme hakkında bilgi edinin.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 886d17098259ddbb78698a3c1280f797e370c714
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597154"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 'de dizin oluşturma ilkeleri

Azure Cosmos DB, her kapsayıcının kapsayıcının öğelerinin nasıl dizine alınacağını belirleyen bir dizin oluşturma ilkesi vardır. Yeni oluşturulan kapsayıcılar için varsayılan dizin oluşturma ilkesi her öğenin her bir özelliğini dizinleyen, herhangi bir dize veya sayı için Aralık dizini uygulayan ve tür noktası olan herhangi bir GeoJSON nesnesi için uzamsal dizinler. Bu, dizin oluşturma ve dizin yönetimi ile ilgili düşünmek zorunda kalmadan yüksek sorgu performansı almanızı sağlar.

Bazı durumlarda, gereksinimlerinize daha iyi uyacak şekilde bu otomatik davranışı geçersiz kılmak isteyebilirsiniz. *Dizin oluşturma modunu*ayarlayarak bir kapsayıcının dizin oluşturma ilkesini özelleştirebilir ve *özellik yollarını*dahil edebilir veya dışlayabilirsiniz.

> [!NOTE]
> Bu makalede açıklanan dizin oluşturma ilkelerini güncelleştirme yöntemi yalnızca Azure Cosmos DB SQL (Core) API 'SI için geçerlidir.

## <a name="indexing-mode"></a>Dizin oluşturma modu

Azure Cosmos DB iki dizin oluşturma modunu destekler:

- **Tutarlı**: öğe oluşturma, güncelleştirme veya silme işlemi sırasında dizin zaman uyumlu olarak güncelleştirilir. Bu, okuma sorgularınızın tutarlılığı, [hesap için yapılandırılmış tutarlılığa](consistency-levels.md)sahip olacağı anlamına gelir.
- **Hiçbiri**: Dizin oluşturma kapsayıcıda devre dışı bırakıldı. Bu genellikle bir kapsayıcı, ikincil dizinlere gerek olmadan saf anahtar-değer deposu olarak kullanıldığında kullanılır. Toplu işlemlerin performansını artırmak için de kullanılabilir. Toplu işlemler tamamlandıktan sonra, dizin modu tutarlı olarak ayarlanabilir ve sonra, Işlem tamamlanana kadar [ındexdönüşümle ilerlemesi](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) kullanılarak izlenebilir.

> [!NOTE]
> Cosmos DB, yavaş dizin oluşturma modunu da destekler. Yavaş dizin oluşturma, altyapı başka bir iş gerçekleştirmediğinden daha düşük bir öncelik düzeyinde dizinde güncelleştirmeler gerçekleştirir. Bu, **tutarsız veya tamamlanmamış** sorgu sonuçlarının oluşmasına neden olabilir. Ayrıca, toplu işlemler için ' none ' yerine yavaş dizin oluşturma özelliği, Dizin modunda yapılan herhangi bir değişiklik dizinin bırakılmasına ve yeniden oluşturulmasına neden olacağı için de hiçbir avantaj sağlamaz. Bu nedenlerden dolayı müşterileri kullanan müşterilere karşı öneririz. Toplu işlemlere yönelik performansı artırmak için, Dizin modunu None olarak ayarlayın ve ardından, tutarlı moda dönün ve işlem tamamlanana kadar kapsayıcıda `IndexTransformationProgress` özelliğini izleyin.

Varsayılan olarak, dizin oluşturma ilkesi `automatic` olarak ayarlanır. Dizin oluşturma ilkesindeki `automatic` özelliği `true` olarak ayarlanarak elde edilir. Bu özelliğin `true` olarak ayarlanması, Azure CosmosDB 'nin belgeleri yazıldığı şekilde otomatik olarak dizin oluşturulmasına olanak sağlar.

## <a name="including-and-excluding-property-paths"></a>Özellik yollarını dahil etme ve hariç tutma

Özel bir dizin oluşturma ilkesi, dizin oluşturma işleminden açıkça dahil edilen veya dışlanan Özellik yollarını belirtebilir. Dizini oluşturulmuş yolların sayısını en iyi duruma getirerek, Kapsayıcınız tarafından kullanılan depolama miktarını düşürebilirsiniz ve yazma işlemlerinin gecikme süresini artırabilirsiniz. Bu yollar, [Dizin oluşturma genel bakış bölümünde açıklanan yöntemi](index-overview.md#from-trees-to-property-paths) aşağıdaki eklemelerle izleyerek tanımlanmıştır:

- skaler bir değere (dize veya sayı) önde gelen yol `/?` ile biter
- bir dizideki öğeler `/[]` gösterimi (`/0` yerine `/1` vb.) ile birlikte karşılanır.
- `/*` joker karakteri, düğümün altındaki herhangi bir öğeyi eşleştirmek için kullanılabilir

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

- `headquarters` `employees` yolu `/headquarters/employees/?`

- `locations` ' `country` yolu `/locations/[]/country/?`

- `headquarters` altında herhangi bir şeyin yolu `/headquarters/*`

Örneğin, `/headquarters/employees/?` yolunu dahil eteceğiz. Bu yol çalışanlar özelliğini dizinliyoruz, ancak bu özellik içinde iç içe geçmiş JSON dizinini dizinliyoruz.

## <a name="includeexclude-strategy"></a>Dahil etme/hariç tutma stratejisi

Herhangi bir dizin oluşturma ilkesinin, kök yolu `/*` dahil edilen ya da hariç tutulmuş bir yol olarak içermesi vardır.

- Dizin oluşturma gerektirmeyen yolları seçmeli olarak hariç tutmak için kök yolu ekleyin. Bu, modelinize eklenebilen yeni bir özelliğin Azure Cosmos DB proaktif olarak dizinlemenizi sağlayan bu, önerilen yaklaşımdır.
- Dizine eklenmesi gereken yolları seçmeli olarak dahil etmek için kök yolu hariç tutun.

- : Alfasayısal karakterler ve _ (alt çizgi) içeren normal karakter içeren yollar için, yol dizesinin çift tırnak etrafında (örneğin, "/path/?") kaçış olması gerekmez. Diğer özel karakterlere sahip yollar için, yol dizesini çift tırnak etrafında (örneğin, "/\"path-ABC \"/?") atlamanız gerekir. Yolunuzda özel karakterler bekleliyorsanız, güvenlik için her yolu da kaçış yapabilirsiniz. İşlevsel olarak, tüm yolları yalnızca özel karakterlere sahip olanlara karşı atladıysanız herhangi bir farklılık yapmaz.

- "ETag" sistem özelliği dizin oluşturma için eklenen yola eklenemediği takdirde varsayılan olarak dizin oluşturma işleminden çıkarılır.

Yolları dahil etme ve hariç tutma sırasında aşağıdaki özniteliklerle karşılaşabilirsiniz:

- `kind` `range` ya da `hash` olabilir. Aralık dizini işlevselliği bir karma dizinin tüm işlevlerini sağlar, bu nedenle bir Aralık dizini kullanmanızı öneririz.

- `precision`, eklenen yollar için dizin düzeyinde tanımlanmış bir sayıdır. @No__t_0 değeri en yüksek duyarlık değerini gösterir. Bu değeri her zaman `-1` olarak ayarlamayı öneririz.

- `dataType` `String` ya da `Number` olabilir. Bu, dizine eklenecek JSON özelliklerinin türlerini gösterir.

Belirtilmediğinde, bu özellikler aşağıdaki varsayılan değerlere sahip olur:

| **Özellik adı**     | **Varsayılan değer** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` ve `Number` |

Yolların dahil edilmesi ve dışlanması için ilke örneklerinin dizinini oluşturma [bölümüne](how-to-manage-indexing-policy.md#indexing-policy-examples) bakın.

## <a name="spatial-indexes"></a>Uzamsal dizinler

Dizin oluşturma ilkesinde bir uzamsal yol tanımladığınızda, hangi dizin ```type``` bu yola uygulanacağını tanımlamanız gerekir. Uzamsal dizinler için olası türler şunlardır:

* Seçeneğinin

* Gen

* MultiPolygon

* LineString

Azure Cosmos DB, varsayılan olarak hiçbir uzamsal dizin oluşturmaz. Uzamsal SQL yerleşik işlevlerini kullanmak isterseniz, gereken özelliklerde bir uzamsal dizin oluşturmanız gerekir. Uzamsal dizinler eklemeye yönelik dizin oluşturma örnekleri için [Bu bölüme](geospatial.md) bakın.

## <a name="composite-indexes"></a>Bileşik dizinler

İki veya daha fazla özelliği olan bir `ORDER BY` yan tümcesine sahip sorgular bileşik bir dizin gerektirir. Ayrıca, birçok eşitlik ve Aralık sorgusunun performansını artırmak için bir bileşik dizin tanımlayabilirsiniz. Varsayılan olarak, bir bileşik dizin tanımlanmadığında, gereken şekilde [Bileşik dizinler eklemelisiniz](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) .

Bileşik dizin tanımlarken şunu belirtirsiniz:

- İki veya daha fazla özellik yolu. Özellik yollarının önemli olarak tanımlandığı sıra.

- Sıra (artan veya azalan).

> [!NOTE]
> Bileşik dizin eklediğinizde, yeni bileşik dizin ekleme tamamlanana kadar sorgu mevcut Aralık dizinlerini kullanır. Bu nedenle, bileşik bir dizin eklediğinizde performans iyileştirmelerini hemen gözlemleyebilirsiniz. [SDK 'lardan birini kullanarak](how-to-manage-indexing-policy.md)Dizin dönüşümünün ilerlemesini izlemek mümkündür.

### <a name="order-by-queries-on-multiple-properties"></a>Birden çok özelliklerde sorguya göre sırala:

İki veya daha fazla özelliği olan bir `ORDER BY` yan tümcesi içeren sorgular için Bileşik dizinler kullanılırken aşağıdaki noktalar kullanılır:

- Bileşik dizin yolları `ORDER BY` yan tümcesindeki özelliklerin dizisiyle eşleşmezse, bileşik dizin sorguyu destekleyemez.

- Bileşik dizin yollarının sırası (artan veya azalan) `ORDER BY` yan tümcesindeki `order` de eşleşmelidir.

- Bileşik dizin Ayrıca tüm yollarda ters sırada olan bir `ORDER BY` yan tümcesini destekler.

Bir bileşik dizinin özellikler adı, yaşı ve _ts üzerinde tanımlandığı aşağıdaki örneği göz önünde bulundurun:

| **Bileşik Dizin**     | **Örnek `ORDER BY` sorgusu**      | **Bileşik dizin tarafından destekleniyor mu?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Tüm gerekli `ORDER BY` sorgularını kullanabilmeniz için dizin oluşturma ilkenizi özelleştirmeniz gerekir.

### <a name="queries-with-filters-on-multiple-properties"></a>Birden fazla özelliğinde filtreler olan sorgular

Bir sorgu iki veya daha fazla özelliğe filtre içeriyorsa, bu özellikler için bileşik bir dizin oluşturmak yararlı olabilir.

Örneğin, iki özellik üzerinde bir eşitlik filtresi bulunan aşağıdaki sorguyu göz önünde bulundurun:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Bu sorgu daha verimli olacaktır, daha az zaman alır ve bir bileşik dizinden yararlanıyorsa daha az RU (ad ASC, Age ASC).

Aralık filtreleri içeren sorgular da bileşik bir dizinle iyileştirilebilir. Ancak, sorgu yalnızca tek bir Aralık filtresine sahip olabilir. Aralık filtreleri `>`, `<`, `<=`, `>=` ve `!=` içerir. Aralık filtresi, en son bileşik dizinde tanımlanmalıdır.

Aşağıdaki sorguyu hem eşitlik hem de Aralık filtreleriyle göz önünde bulundurun:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Bu sorgu, üzerinde bileşik bir dizin ile daha verimli olacaktır (ad ASC, Age ASC). Ancak, sorgu (Age ASC, ad ASC) üzerinde bir bileşik dizin kullanmaz, çünkü eşitlik filtreleri ilk olarak bileşik dizinde tanımlanmalıdır.

Birden çok özelliklerde filtre içeren sorgular için Bileşik dizinler oluşturulurken aşağıdaki noktalar kullanılır

- Sorgunun filtresindeki özellikler, bileşik dizinindekilerle eşleşmelidir. Bir özellik bileşik dizindaysa, ancak sorguya filtre olarak eklenmemelidir, sorgu bileşik dizinden yararlanmaz.
- Bir sorguda, bir bileşik dizinde tanımlanmayan ek özellikler varsa, sorguyu değerlendirmek için bileşik ve Aralık dizinlerinin bir birleşimi kullanılır. Bu, Aralık dizinleri kullanılarak özel olarak daha az RU gerektirir.
- Bir özelliğin Aralık filtresi varsa (`>`, `<`, `<=`, `>=` veya `!=`), bu özellik bileşik dizinde en son tanımlanmalıdır. Bir sorguda birden fazla Aralık filtresi varsa, bileşik dizinden yararlanmaz.
- Birden çok filtre içeren sorguları iyileştirmek için bir bileşik dizin oluştururken, bileşik dizinin `ORDER` sonuçları üzerinde hiçbir etkisi olmaz. Bu özellik isteğe bağlıdır.
- Birden çok özelliklerde filtre içeren bir sorgu için bileşik dizin tanımlamadıysanız sorgu yine de başarılı olur. Ancak, sorgunun RU maliyeti bir bileşik dizinle azaltılabilir.

Bir bileşik dizinin özellikler adı, yaşı ve zaman damgasında tanımlandığı aşağıdaki örnekleri göz önünde bulundurun:

| **Bileşik Dizin**     | **Örnek sorgu**      | **Bileşik dizin tarafından destekleniyor mu?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Filtresi ve ORDER BY yan tümcesi olan sorgular

Bir sorgu bir veya daha fazla özellik üzerinde filtreleyip ORDER BY yan tümcesinde farklı özelliklere sahipse, filtrenin özelliklerini `ORDER BY` yan tümcesine eklemek yararlı olabilir.

Örneğin, filtreye ORDER BY yan tümcesine eklenen özellikleri ekleyerek, bir bileşik dizinden yararlanmak için aşağıdaki sorgu yeniden yazılabilir:

Aralık dizinini kullanan sorgu:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Bileşik dizin kullanarak sorgula:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Aynı model ve sorgu iyileştirmeleri, birden çok eşitlik filtresi içeren sorgular için genelleştirilerek bulunabilir:

Aralık dizinini kullanan sorgu:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Bileşik dizin kullanarak sorgula:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Bir sorguyu bir filtre ve `ORDER BY` yan tümcesiyle iyileştirmek için Bileşik dizinler oluşturulurken aşağıdaki noktalar kullanılır:

* Sorgu, özelliklere filtre uygular, bu, ilk olarak `ORDER BY` yan tümcesine eklenmelidir.
* Bir özellik üzerinde filtreye sahip bir sorgu üzerinde bir bileşik dizin tanımlamadıysanız ve farklı bir özellik kullanarak ayrı bir `ORDER BY` yan tümcesi, sorgu yine de başarılı olur. Ancak, özellikle de `ORDER BY` yan tümcesindeki özelliğin yüksek bir kardinalite özelliği varsa, sorgunun RU maliyeti bileşik bir dizinle azaltılabilir.
* Birden fazla özelliğe sahip `ORDER BY` sorguları ve birden çok özelliklerde filtreleri olan sorgular için Bileşik dizinler oluşturmaya yönelik tüm hususlar hala geçerlidir.


| **Bileşik Dizin**                      | **Örnek `ORDER BY` sorgusu**                                  | **Bileşik dizin tarafından destekleniyor mu?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Dizin oluşturma ilkesini değiştirme

Kapsayıcının dizin oluşturma ilkesi [, Azure Portal veya desteklenen SDK 'lardan birini kullanarak](how-to-manage-indexing-policy.md)dilediğiniz zaman güncelleştirilemeyebilir. Dizin oluşturma ilkesi için bir güncelleştirme, eski dizinden yeni bir dönüştürmeyi tetikler ve bu, çevrimiçi ve yerinde gerçekleştirilir (Bu nedenle işlem sırasında ek depolama alanı tüketilmelidir). Eski ilkenin dizini, yazma kullanılabilirliğini etkilemeden ve kapsayıcıda sağlanan aktarım hızını etkilemeden etkili bir şekilde yeni ilkeye dönüştürülür. Dizin dönüştürme zaman uyumsuz bir işlemdir ve tamamlanmak üzere gereken süre, sağlanan aktarım hızına, öğe sayısına ve bunların boyutuna bağlıdır.

> [!NOTE]
> Bir Aralık veya uzamsal dizin eklenirken, sorgular tüm eşleşen sonuçları döndürmeyebilir ve hata döndürmeksizin bunu yapacaktır. Bu, sorgu sonuçlarının Dizin dönüştürmesi tamamlanana kadar tutarlı olamayacağı anlamına gelir. [SDK 'lardan birini kullanarak](how-to-manage-indexing-policy.md)Dizin dönüşümünün ilerlemesini izlemek mümkündür.

Yeni dizin oluşturma ilkesinin modu tutarlı olarak ayarlandıysa, Dizin dönüştürme işlemi devam ederken başka bir dizin oluşturma ilkesi değişikliği uygulanabilir. Çalışan bir dizin dönüştürmesi, dizin oluşturma ilkesinin modu None olarak ayarlanarak iptal edilebilir (Bu, dizini hemen bırakacak).

## <a name="indexing-policies-and-ttl"></a>Dizin oluşturma ilkeleri ve TTL

[Yaşam süresi (TTL) özelliği](time-to-live.md) , dizin oluşturmanın açık olduğu kapsayıcıda etkin olmasını gerektirir. Bunun anlamı:

- Dizin oluşturma modunun None olarak ayarlandığı bir kapsayıcıda TTL 'yi etkinleştirmek mümkün değildir,
- TTL 'nin etkinleştirildiği bir kapsayıcıda dizin oluşturma modunun hiçbiri olarak ayarlanması mümkün değildir.

Hiçbir özellik yolunun dizine alınması gereken ancak TTL 'nin gerekli olduğu senaryolarda, ile bir dizin oluşturma ilkesi kullanabilirsiniz:

- bir dizin oluşturma modu tutarlı olarak ayarlanır ve
- dahil edilen yol yok ve
- yalnızca Dışlanan yol olarak `/*`.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde dizin oluşturma hakkında daha fazla bilgi edinin:

- [Dizine genel bakış](index-overview.md)
- [Dizin oluşturma ilkesini yönetme](how-to-manage-indexing-policy.md)
