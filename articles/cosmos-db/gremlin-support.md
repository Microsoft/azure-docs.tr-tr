---
title: Inkerpop özellikleriyle Azure Cosmos DB Gremlin desteği ve uyumluluğu
description: Apache TinkerPop’un Gremlin dili hakkında bilgi edinin. Azure Cosmos DB ve TinkerPop grafik Altyapısı uyumluluk farkları ' nda hangi özellik ve adımların kullanılabildiğini öğrenin.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: 036338e90a3e7b466924d419400c0dcc692dec5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630760"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Inkerpop özellikleriyle Azure Cosmos DB Gremlin Graph desteği ve uyumluluğu
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB, [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps)olarak bilinen [Apache tinkerpop 'un](https://tinkerpop.apache.org) grafik çapraz geçiş dilini destekler. Grafik varlıkları (köşeler ve kenarlar) oluşturmak, bu varlıkların içindeki özellikleri değiştirmek, sorgu ve geçiş işlemleri gerçekleştirmek ve varlıkları silmek için Gremlin dilini kullanabilirsiniz.

Azure Cosmos DB Graph Engine, [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) geçiş adımları belirtimini yakından izler, ancak uygulamada Azure Cosmos DB özgü farklılıklar vardır. Bu makalede, Gremlin için hızlı bir yol sunuyoruz ve Gremlin API tarafından desteklenen Gremlin özelliklerini listeliyoruz.

## <a name="compatible-client-libraries"></a>Uyumlu istemci kitaplıkları

Aşağıdaki tabloda Azure Cosmos DB’ye karşı kullanabileceğiniz popüler Gremlin sürücüleri gösterilir:

| İndir | Kaynak | Kullanmaya Başlama | Desteklenen bağlayıcı sürümü |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [GitHub’da Gremlin.NET](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [.NET kullanarak Grafik oluşturma](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Java kullanarak Grafik oluşturma](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [GitHub’da Gremlin-JavaScript](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Node.js kullanarak Grafik oluşturma](create-graph-nodejs.md) | 3.3.4 + |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python on GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Python kullanarak Grafik oluşturma](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Github'da Gremlin-PHP](https://github.com/PommeVerte/gremlin-php) | [PHP kullanarak Grafik oluşturma](create-graph-php.md) | 3.1.0 |
| [Git lang](https://github.com/supplyon/gremcos/) | [Git lang](https://github.com/supplyon/gremcos/) | | Bu kitaplık, dış katkıda bulunanlar tarafından oluşturulmuştur. Azure Cosmos DB takım herhangi bir destek sunmaz veya kitaplığı korumaz. |
| [Gremlin konsolu](https://tinkerpop.apache.org/downloads.html) | [TinkerPop belgeleri](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Gremlin konsolunu kullanarak Grafik oluşturma](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Desteklenen grafik nesneleri

TinkerPop, çeşitli grafik teknolojilerini kapsayan bir standarttır. Bu nedenle bir grafik sağlayıcısı tarafından sağlanan özellikleri tanımlamaya yönelik standart bir terminolojisi vardır. Azure Cosmos DB kalıcı, yüksek eşzamanlılığa sahip, birden çok sunucu ve kümeye ayrılabilen yazılabilir bir grafik veritabanı sağlar. 

Aşağıdaki tabloda Azure Cosmos DB tarafından uygulanan TinkerPop özellikleri listelenmektedir: 

| Kategori | Azure Cosmos DB uygulaması |  Notlar | 
| --- | --- | --- |
| Grafik özellikleri | Kalıcılık ve EşzamanlıErişim sağlar. İşlemleri desteklemek için tasarlanmıştır | Bilgisayar yöntemleri, Spark bağlayıcısı tarafından uygulanabilir. |
| Değişken özellikleri | Boolean, Tamsayı, Bayt, Çift, Kayan Sayı, Uzun, Dize destekler | İlkel türleri destekler ve veri modeli aracılığıyla oluşan karmaşık türlerle uyumludur |
| Köşe özellikleri | RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty işlevlerini destekler  | Köşe oluşturma, değiştirme ve silmeyi destekler |
| Köşe özellikleri | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues işlevlerini destekler | Köşe özelliklerini oluşturma, değiştirme ve silmeyi destekler |
| Kenar özellikleri | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Kenar oluşturma, değiştirme ve silmeyi destekler |
| Kenar özellikleri | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Kenar özelliklerini oluşturma, değiştirme ve silmeyi destekler |

## <a name="gremlin-wire-format"></a>Gremlin Tel biçimi

Azure Cosmos DB Gremlin işlemlerinden sonuçları döndürürken JSON biçimini kullanır. Azure Cosmos DB Şu anda JSON biçimini destekliyor. Örneğin, aşağıdaki kod parçacığında Azure Cosmos DB *istemciye döndürülen* BIR köşesinin JSON temsili gösterilmektedir:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Köşelere yönelik JSON biçimi tarafından kullanılan özellikler aşağıda açıklanmıştır:

| Özellik | Açıklama | 
| --- | --- | --- |
| `id` | Köşenin kimliği. Benzersiz olmalıdır (varsa değeriyle birlikte `_partition` ). Değer sağlanmazsa, otomatik olarak bir GUID ile sağlanacaktır | 
| `label` | Köşenin etiketi. Bu özellik varlık türünü tanımlamakta kullanılır. |
| `type` | Grafik olmayan belgelerdeki köşeleri ayırt etmek için kullanılır |
| `properties` | Köşe ile ilişkili, kullanıcı tanımlı özellikler paketi. Her bir özellik birden çok değere sahip olabilir. |
| `_partition` | Köşenin bölüm anahtarı. [Grafik bölümlendirme](graph-partitioning.md)için kullanılır. |
| `outE` | Bu özellik, bir köşenin giden kenarlarının bir listesini içerir. Komşuluk bilgilerini köşeyle birlikte depolamak, geçişlerin hızla yürütülmesini sağlar. Kenarlar etiketlerine göre gruplandırılır. |

Kenar, grafiğin diğer bölümlerine gezintiyi kolaylaştırmak için aşağıdaki bilgiyi içerir.

| Özellik | Açıklama |
| --- | --- |
| `id` | Kenarın kimliği. Benzersiz olmalıdır (varsa değeriyle birlikte `_partition` ) |
| `label` | Kenarın etiketi. Bu özellik isteğe bağlıdır ve ilişki türünü tanımlamak için kullanılır. |
| `inV` | Bu özellik, bir kenar için köşelerin bir listesini içerir. Komşuluk bilgilerini kenarla birlikte depolamak, geçişlerin hızla yürütülmesini sağlar. Köşeler etiketlerine göre gruplandırılır. |
| `properties` | Kenar ile ilişkili, kullanıcı tanımlı özellikler paketi. Her bir özellik birden çok değere sahip olabilir. |

Her bir özellik, bir dizi içinde birden çok değer depolayabilir. 

| Özellik | Açıklama |
| --- | --- |
| `value` | Özelliğin değeri

## <a name="gremlin-steps"></a>Gremlin adımları

Şimdi de Azure Cosmos DB tarafından desteklenen Gremlin adımlarına bakalım. Gremlin hakkında eksiksiz bir başvuru için bkz. [TinkerPop başvurusu](https://tinkerpop.apache.org/docs/3.3.2/reference).

| adım | Description | TinkerPop 3.2 Belgeleri |
| --- | --- | --- |
| `addE` | İki köşe arasına kenar ekler | [addE step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Grafiğe bir köşe ekler | [addV step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Tüm geçişlerin bir değer döndürmesini sağlar | [and step](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Bir adımın çıktısına değişken atanmasını sağlayan adım modülatörü | [as step](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | `group` ve `order` ile kullanılan bir adım modülatörü | [by step](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Sonuç döndüren ilk geçişi döndürür | [coalesce step](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Sabit bir değer döndürür. `coalesce` ile kullanılır| [constant step](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Geçiş sayımını döndürür | [count step](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Yinelenenlerin kaldırıldığı değerleri döndürür | [dedup step](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Değerleri (köşe/kenar) bırakır | [drop step](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Yürütülen Gremlin adımı tarafından oluşturulan tüm işlemlerin açıklamasını oluşturur | [executionProfile adımı](graph-execution-profile.md) |
| `fold` | Sonuçların toplamını hesaplayan bir engel gibi davranır| [fold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Belirtilen etiketleri temel alarak değerleri gruplandırır| [group step](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Özellikleri, köşeleri ve kenarları filtrelemek için kullanılır. `hasLabel`, `hasId`, `hasNot` ve `has` değişkenlerini destekler. | [has step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Değerleri bir akışa ekler| [inject step](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Boole ifadesi kullanarak bir filtre uygulamak için kullanılır | [is step](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Geçişteki öğelerin sayısını sınırlamak için kullanılır| [limit step](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Alt sorgu gibi, geçişin bir bölümünü yerel olarak sarmalar | [local step](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Filtre olumsuzlamayı üretmek için kullanılır | [not step](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Bir sonuç elde ettiği takdirde, belirtilen geçişin sonucunu döndürür; aksi takdirde çağıran öğeyi döndürür | [isteğe bağlı adım](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | En azından bir geçişin değer döndürmesini sağlar | [or step](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Sonuçları, belirtilen sıralama düzeninde döndürür | [order step](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Geçişin tam yolunu döndürür | [path step](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Özellikleri bir Harita gibi projelendirir | [project step](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Belirtilen etiketlerin özelliklerini döndürür | [properties step](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Belirtilen değer aralığını filtreler| [range step](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Adımı belirtilen sayıda tekrarlar. Döngü için kullanılır | [repeat step](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Sonuçları geçişten örneklendirmek için kullanılır | [sample step](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Sonuçları geçişten projelendirmek için kullanılır |  [select step](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Geçişteki engelleyici olmayan toplamalar için kullanılır | [store step](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Dize filtreleme işlevi. Bu işlev, bir `has()` özelliğin belirli bir dizenin başlangıcıyla eşleşmesi için bir koşul olarak kullanılır | [TextP koşulları](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Dize filtreleme işlevi. Bu işlev, `has()` belirli bir dizenin sonu ile bir özelliği eşleştirmek için bir koşul olarak kullanılır | [TextP koşulları](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Dize filtreleme işlevi. Bu işlev, `has()` belirli bir dizenin içeriğiyle bir özelliği eşlemek için adım için bir koşul olarak kullanılır | [TextP koşulları](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Dize filtreleme işlevi. Bu işlev, `has()` belirli bir dize ile başlamabir özelliği eşlemek için adım için bir koşul olarak kullanılır | [TextP koşulları](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Dize filtreleme işlevi. Bu işlev, `has()` belirli bir dizeyle bitmeyen bir özelliği eşleştirmek için bir koşul olarak kullanılır | [TextP koşulları](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Dize filtreleme işlevi. Bu işlev, `has()` belirli bir dize içermeyen bir özelliği eşleştirmek için bir koşul olarak kullanılır | [TextP koşulları](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Bir köşeden ağaca yolları toplar | [tree step](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Adım olarak bir yineleyici açar| [unfold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Birden çok geçişin sonuçlarını birleştirir| [union step](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Köşe ve kenarlar arasında geçiş için gerekli olan adımları içerir: `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` ve `otherV` | [vertex steps](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Geçişten alınan sonuçları filtrelemek için kullanılır. `eq`, `neq`, `lt`, `lte`, `gt`, `gte` ve `between` işleçlerini destekler  | [where step](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Azure Cosmos DB tarafından sağlanan, yazma için iyileştirilmiş altyapı, köşe ve kenarlar içindeki tüm özelliklerin dizinlerinin otomatik olarak oluşturulmasını varsayılan olarak destekler. Bu nedenle herhangi bir özellik üzerindeki sorgulu filtreler, aralık sorguları, sıralama veya toplamalar dizinden işlenir ve etkin bir biçimde sunulur. Azure Cosmos DB’de dizin oluşturmanın işleyişi hakkında daha fazla bilgi için [schema-agnostic dizin oluşturma](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) makalemizi okuyun.

## <a name="behavior-differences"></a>Davranış farklılıkları

* Azure Cosmos DB Graph Engine, ınkerpop Gremlin 'ın derinliği ilk kez olacak şekilde, ***ilk*** çapraz geçişi çalıştırır. Bu davranış, Cosmos DB benzer şekilde yatay ölçeklenebilir sistemde daha iyi performans elde eder.

## <a name="unsupported-features"></a>Desteklenmeyen özellikler

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** graf dolaşma işlemlerine yönelik programlama dilinden bağımsız bir belirtimdir. Cosmos DB Graph henüz desteklemiyor. `GremlinClient.SubmitAsync()`Geçişi bir metin dizesi olarak kullanın ve geçirin.

* ***`property(set, 'xyz', 1)`*** kardinalite kümesi bugün desteklenmiyor. Bunun yerine `property(list, 'xyz', 1)` kullanın. Daha fazla bilgi için bkz. [ınkerpop Ile köşe özellikleri](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`match()` Adım*** Şu anda kullanılamıyor. Bu adım bildirime dayalı sorgulama özellikleri sağlar.

* Köşelerin veya kenarlardaki ***Özellikler olarak nesneler*** desteklenmez. Özellikler yalnızca temel türler veya diziler olabilir.

* ***Dizi özelliklerine*** `order().by(<array property>)` göre sıralama desteklenmez. Yalnızca temel türlere göre sıralama desteklenir.

* ***Ilkel olmayan JSON türleri*** desteklenmez. `string`, `number` Veya türlerini kullanın `true` / `false` . `null` değerler desteklenmez. 

* ***GraphSONv3*** seri hale getirici Şu anda desteklenmiyor. `GraphSONv2`Bağlantı yapılandırmasındaki serileştirici, okuyucu ve yazıcı sınıflarını kullanın. Azure Cosmos DB Gremlin API tarafından döndürülen sonuçlar GraphSON biçimiyle aynı biçimde değil. 

* **Lambda ifadeleri ve işlevleri** Şu anda desteklenmemektedir. Bu,, `.map{<expression>}` `.by{<expression>}` ve `.filter{<expression>}` işlevlerini içerir. Daha fazla bilgi edinmek ve Gremlin adımlarını kullanarak bunları yeniden yazmayı öğrenmek için [Lambdalar hakkında bir nota](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)bakın.

* Sistemin dağıtılmış doğası nedeniyle ***işlemler*** desteklenmiyor.  Gremlin hesabında uygun tutarlılık modelini "kendi yazınızla okumak" üzere yapılandırın ve çakışan yazmaları çözümlemek için iyimser eşzamanlılık kullanın.

## <a name="known-limitations"></a>Bilinen sınırlamalar

* **Orta çapraz geçiş `.V()` adımlarıyla Gremlin sorguları için dizin kullanımı**: Şu anda, `.V()` bir geçiş geçişinin yalnızca ilk çağrısı, bu dizine iliştirilmiş tüm filtre ve koşullara çözüm sağlamak için dizini kullanır. Sonraki çağrılar dizine danışmayacak, bu da sorgunun gecikmesini ve maliyetini artırabilir.
    
Varsayılan dizin oluşturma varsayılırsa, adımla başlayan tipik bir okunan Gremlin sorgusu, `.V()` `.has()` `.where()` sorgu maliyetini ve performansını iyileştirmek için veya gibi ilgili filtreleme adımlarında parametreleri kullanacaktır. Örnek:

```java
g.V().has('category', 'A')
```

Ancak, `.V()` Gremlin sorgusuna birden fazla adım eklendiğinde, sorgu verilerinin çözümlenmesi en iyi durumda olmayabilir. Örnek olarak aşağıdaki sorguyu gerçekleştirin:

```java
g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
```

Bu sorgu, adlandırılan özelliklerine göre iki köşe grubu döndürür `category` . Bu durumda, yalnızca ilk çağrı, `g.V().has('category', 'A')` özelliklerinin değerlerine göre köşeleri çözümlemek için dizini kullanır.

Bu sorguya yönelik bir geçici çözüm, ve gibi subtraversal adımlarını kullanmaktır `.map()` `union()` . Bu, aşağıda belirtilen şekilde belirlenir:

```java
// Query workaround using .map()
g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

// Query workaround using .union()
g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
```

[Gremlin `executionProfile()` adımını](graph-execution-profile.md)kullanarak sorguların performansını gözden geçirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [SDK’larımızı kullanarak](create-graph-dotnet.md) bir grafik uygulaması oluşturmaya başlayın 
* Azure Cosmos DB’de [grafik desteği](graph-introduction.md) hakkında daha fazla bilgi edinin
