---
title: Azure Cosmos DB Gremlin API 'sindeki sorguları değerlendirmek için yürütme profilini kullanın
description: Yürütme profili adımını kullanarak Gremlin sorgularını nasıl giderebileceğinizi ve geliştireceğinizi öğrenin.
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
ms.author: chrande
ms.openlocfilehash: 18cefb1dd80368a8ccdad9f6f3ffc30881a8a889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087494"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Gremlin sorgularınızı hesaplamak için yürütme profili adımını kullanma
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Bu makale, Azure Cosmos DB Gremlin API’si graf veritabanları için yürütme profilini kullanma adımı için bir genel bakış sunar. Bu adım, sorun giderme ve sorgu iyileştirmeleri için ilgili bilgileri sağlar ve Cosmos DB Gremlin API’si hesabında yürütülebilen tüm Gremlin sorgularıyla uyumludur.

Bu adımı kullanmak için, yalnızca `executionProfile()` Gremlin sorgusunun sonundaki işlev çağrısını ekleyin. **Gremlin sorgunuz yürütülecektir** ve işlemin sonucu sorgu yürütme PROFILIYLE bir JSON yanıt nesnesi döndürür.

Örnek:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Adımı çağırdıktan sonra `executionProfile()` , yanıt yürütülen Gremlin adımını, aldığı toplam süreyi ve deyimin sonuçlandığı Cosmos DB çalışma zamanı işleçleri dizisini içeren BIR JSON nesnesi olacaktır.

> [!NOTE]
> Yürütme profili için bu uygulama Apache Tinkerpop belirtiminde tanımlı değil. Azure Cosmos DB Gremlin API 'sinin uygulamasına özgüdür.


## <a name="response-example"></a>Yanıt örneği

Aşağıda, döndürülecek çıkışın ek açıklamalı bir örneği verilmiştir:

> [!NOTE]
> Bu örneğe, yanıtın genel yapısını açıklayan yorumlarla açıklama eklenir. Gerçek bir executionProfile yanıtı hiç yorum içermemelidir.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> ExecutionProfile adımı Gremlin sorgusunu yürütür. Bu, `addV` veya adımlarını içerir, bu da `addE` oluşturma ile sonuçlanır ve sorguda belirtilen değişiklikleri yürütmeyecektir. Sonuç olarak, Gremlin sorgusu tarafından oluşturulan Istek birimleri de ücretlendirilecektir.

## <a name="execution-profile-response-objects"></a>Yürütme profili yanıt nesneleri

Bir executionProfile () işlevinin yanıtı, aşağıdaki yapıyla bir JSON nesneleri hiyerarşisi sağlayacak:
  - **Gremlin Operation nesnesi**: yürütülen tüm Gremlin işlemini temsil eder. Aşağıdaki özellikleri içerir.
    - `gremlin`: Yürütülen açık Gremlin bildirisi.
    - `totalTime`: Bu adımın yürütülmesi için milisaniye cinsinden süre. 
    - `metrics`: Sorguyu yerine getirmek için yürütülen Cosmos DB çalışma zamanı işleçlerini her birini içeren bir dizi. Bu liste, yürütme sırasına göre sıralanır.
    
  - **Cosmos DB çalışma zamanı işleçleri**: tüm Gremlin işleminin bileşenlerinin her birini temsil eder. Bu liste, yürütme sırasına göre sıralanır. Her nesne aşağıdaki özellikleri içerir:
    - `name`: İşlecin adı. Bu, değerlendirilen ve yürütülen adımın türüdür. Aşağıdaki tabloda daha fazla bilgi edinin.
    - `time`: Belirli bir operatör tarafından geçen süre (milisaniye olarak).
    - `annotations`: Yürütülen işlece özgü ek bilgiler içerir.
    - `annotations.percentTime`: Belirli bir işleci yürütmek için geçen toplam sürenin yüzdesi.
    - `counts`: Bu işleç tarafından depolama katmanından döndürülen nesne sayısı. Bu, `counts.resultCount` içindeki skaler değerde bulunur.
    - `storeOps`: Bir veya birden çok bölüme yayılabilen bir depolama işlemini temsil eder.
    - `storeOps.fanoutFactor`: Bu belirli depolama işleminin eriştiği bölüm sayısını temsil eder.
    - `storeOps.count`: Bu depolama işleminin döndürdüğü sonuç sayısını temsil eder.
    - `storeOps.size`: Belirli bir depolama işleminin sonucunun bayt cinsinden boyutunu temsil eder.

Cosmos DB Gremlin çalışma zamanı Işleci|Description
---|---
`GetVertices`| Bu adım, kalıcılık katmanından tahmine dayalı bir nesne kümesi edinir. 
`GetEdges`| Bu adım bir köşe kümesine bitişik olan kenarları elde eder. Bu adım bir veya daha fazla depolama işlemine yol açabilir.
`GetNeighborVertices`| Bu adım bir kenar kümesine bağlı köşeleri edinir. Kenarlar, hem kaynak hem de hedef köşelerinin bölüm anahtarlarını ve KIMLIĞINI içerir.
`Coalesce`| Bu adım, her `coalesce()` Gremlin adımı yürütüldüğünde iki işlem değerlendirmesi için hesaplar.
`CartesianProductOperator`| Bu adım iki veri kümesi arasında bir Kartezyen ürün hesaplar. Genellikle koşullar `to()` veya her kullanıldığında yürütülür `from()` .
`ConstantSourceOperator`| Bu adım, sonuç olarak sabit bir değer üretmek için bir ifade hesaplar.
`ProjectOperator`| Bu adım, önceki işlemlerin sonucunu kullanarak bir yanıtı hazırlar ve seri hale getirir.
`ProjectAggregation`| Bu adım bir toplam işlem için yanıtı hazırlar ve seri hale getirir.

> [!NOTE]
> Yeni işleçler eklendikçe bu liste güncellenmeye devam edecektir.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Bir yürütme profili yanıtının nasıl çözümlenmesiyle ilgili örnekler

Aşağıda, yürütme profili yanıtını kullanarak sposıya yönelik genel iyileştirmelerin örnekleri verilmiştir:
  - Gizli olmayan fan sorgusu.
  - Filtrelenmemiş sorgu.

### <a name="blind-fan-out-query-patterns"></a>Gizli olmayan fan sorgu desenleri

**Bölümlenmiş bir grafikten** aşağıdaki yürütme profili yanıtını varsayın:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Aşağıdaki ekibinizle şunlardan yapılabilir:
- Gremlin ifadesinin deseninin izlediği için sorgu tek bir KIMLIK aramadır `g.V('id')` .
- `time`Ölçüden sorumlu olan bu sorgunun gecikmesi, [tek bir nokta okuma işlemi için 10ms 'den fazla](./introduction.md#guaranteed-speed-at-any-scale)olduğundan, bu sorgunun gecikmesi yüksek gibi görünüyor.
- Nesnesine baktığımızda, olduğunu, yani `storeOps` `fanoutFactor` `5` [5 bölüme](./partitioning-overview.md) bu işlem tarafından erişildiğini görebiliriz.

Bu çözümlemenin bir sonucu olarak, ilk sorgunun gerekenden daha fazla bölüme erişimi olduğunu belirleyebiliriz. Bu, sorguda bir koşul olarak bölümleme anahtarı belirtilerek çözülebilir. Bu, sorgu başına daha az gecikme süresine ve maliyeti azaltır. [Grafik bölümlendirme](graph-partitioning.md)hakkında daha fazla bilgi edinin. Daha iyi bir sorgu olabilir `g.V('tt0093640').has('partitionKey', 't1001')` .

### <a name="unfiltered-query-patterns"></a>Filtrelenmemiş sorgu desenleri

Aşağıdaki iki yürütme profili yanıtını karşılaştırın. Kolaylık olması için, bu örnekler tek bir bölümlenmiş grafik kullanır.

Bu ilk sorgu, etikete sahip tüm köşeleri alır `tweet` ve sonra komşu köşeleri edinir:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Bitişik köşeleri araştırmadan önce, aynı sorgunun, ancak artık ek bir filtreye sahip profile dikkat edin `has('lang', 'en')` :

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Bu iki sorgu aynı sonuca ulaştı, ancak, bitişik öğeleri sorgulamadan önce daha büyük bir ilk veri kümesini yinelemek gerektiğinden, ilki daha fazla Istek birimi gerektirir. Her iki yanıtlardan de aşağıdaki parametreleri karşılaştırırken, bu davranışın göstergelerini görebiliriz:
- `metrics[0].time`Değer ilk yanıtta daha yüksektir ve bu, tek adımın çözülmesi daha uzun sürdüğünü gösterir.
- `metrics[0].counts.resultsCount`Bu değer, ilk yanıt veri kümesinin daha büyük olduğunu gösteren ilk yanıtın yanı sıra daha yüksektir.

## <a name="next-steps"></a>Sonraki adımlar
* Azure Cosmos DB [desteklenen Gremlin özellikleri](gremlin-support.md) hakkında bilgi edinin. 
* [Azure Cosmos DB 'Da Gremlin API 'si](graph-introduction.md)hakkında daha fazla bilgi edinin.