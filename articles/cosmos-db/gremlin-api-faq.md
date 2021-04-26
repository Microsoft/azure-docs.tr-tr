---
title: Azure Cosmos DB 'da Gremlin API 'SI hakkında sık sorulan sorular
description: Azure Cosmos DB 'da Gremlin API 'SI hakkında sık sorulan soruların yanıtlarını alın
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359797"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Azure Cosmos DB 'da Gremlin API 'SI hakkında sık sorulan sorular
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Bu makalede, Azure Cosmos DB 'de Gremlin API hakkında sık sorulan bazı soruların yanıtları açıklanmaktadır.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Gremlin sorgularının verimliliğini değerlendirme

**Executionprofile ()** önizleme adımı, sorgu yürütme planının analizini sağlamak için kullanılabilir. Bu adımın, aşağıdaki örnekte gösterildiği gibi herhangi bir Gremlin sorgusunun sonuna eklenmesi gerekir:

**Sorgu örneği**

```
g.V('mary').out('knows').executionProfile()
```

**Örnek çıkış**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Yukarıdaki profilin çıktısı, köşe nesneleri, uç nesneler ve çalışma verileri kümesinin boyutunu almak için ne kadar zaman harcandığını gösterir. Bu, Azure Cosmos DB sorguları için standart maliyet ölçümlerle ilgilidir.

## <a name="other-frequently-asked-questions"></a>Diğer sık sorulan sorular

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Grafik veritabanında sorgular çalıştırılırken RU/s nasıl ücretlendirilir?

Tüm Graph nesneleri, köşeleri ve kenarları, arka uçta JSON belgeleri olarak gösterilir. Bir Gremlin sorgusu tek seferde bir veya birden çok grafik nesnesini değiştirebileceğinizden, onunla ilişkili maliyet, sorgu tarafından işlenen nesnelerle, kenarlarla doğrudan ilgilidir. Bu, Azure Cosmos DB diğer tüm API 'Ler için kullanılan işlemdir. Daha fazla bilgi için bkz. [Azure Cosmos DB'de İstek Birimleri](request-units.md).

RU ücreti, sonuç kümesi değil, geçiş geçişinin çalışma verileri kümesine bağlıdır. Örneğin, bir sorgu bir sonuç olarak tek bir köşe elde etmek, ancak aynı şekilde birden fazla başka nesne arasında geçiş yapması gerekiyorsa, maliyet, bir sonuç izdüşümünü hesaplamak için gereken tüm grafik nesnelerine göre yapılır.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Grafik veritabanının Azure Cosmos DB Gremlin API 'de sahip olduğu en büyük ölçek nedir?

Azure Cosmos DB, depolama ve aktarım hızı gereksinimlerinde artışı otomatik olarak çözmek için [Yatay bölümleme](partitioning-overview.md) kullanımını sağlar. Bir iş yükünün en yüksek aktarım hızı ve depolama kapasitesi, belirli bir kapsayıcı ile ilişkili bölüm sayısına göre belirlenir. Ancak, bir Gremlin API kapsayıcısı, uygun bir performans deneyimini ölçeklendirmek için belirli bir kılavuz kümesine sahiptir. Bölümlendirme ve en iyi uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB makalesinde bölümlendirme](partitioning-overview.md) .

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>C#/.NET geliştirme için Microsoft. Azure. Graf paketini veya Gremlin.NET mi kullanmalıyım?

Azure Cosmos DB Gremlin API 'SI, açık kaynaklı sürücülerden hizmete yönelik ana bağlayıcılar olarak yararlanır. Bu nedenle önerilen seçenek [Apache Tinkerpop tarafından desteklenen sürücüleri](https://tinkerpop.apache.org/)kullanmaktır.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Gremlin sürücülerini kullanarak ekleme saldırılarına karşı nasıl koruyabilirim?

Çoğu yerel Apache Tinkerpop Gremlin sürücüleri, sorgu yürütme için bir parametre sözlüğü sağlama seçeneğine izin verir. Bu, [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) Içinde ve [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js)içinde nasıl yapılacağını gösteren bir örnektir.

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Neden "Gremlin sorgu derleme hatası: herhangi bir yöntem bulunamıyor" hatası alıyorum?

Gremlin API Azure Cosmos DB Gremlin Surface alanında tanımlanan işlevselliğin bir alt kümesini uygular. Desteklenen adımlar ve daha fazla bilgi için bkz. [Gremlin destek](gremlin-support.md) makalesi.

En iyi geçici çözüm, gerekli Gremlin adımlarını desteklenen işlevlerle yeniden yazmaktır, çünkü tüm Essential Gremlin adımları Azure Cosmos DB tarafından desteklenir.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Neden "WebSocketException: sunucu, ' 101 ' durum kodu beklenirken ' 200 ' durum kodunu döndürdü" hatasını alıyorum?

Yanlış uç nokta kullanılırken bu hata büyük olasılıkla oluşur. Bu hatayı üreten uç nokta aşağıdaki düzene sahiptir:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Bu, grafik veritabanınızın belge uç noktasıdır.  Kullanılacak doğru uç nokta, aşağıdaki biçime sahip Gremlin uç noktasıdır:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>"RequestRateIsTooLarge" hatasını neden alıyorum?

Bu hata, saniye başına ayrılan Istek birimlerinin sorguya sunacak kadar yeterli olmadığı anlamına gelir. Bu hata genellikle tüm köşeleri elde eden bir sorgu çalıştırdığınızda görülür:

```
// Query example:
g.V()
```

Bu sorgu, grafikteki tüm köşeleri almayı deneyecek. Bu nedenle, bu sorgunun ücreti, ru 'daki en az köşe sayısına eşit olacaktır. RU/s ayarı bu sorguyu ele almak için ayarlanmalıdır.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Gremlin sürücü bağlantılarım neden sonunda kesiliyor?

Bir WebSocket bağlantısı aracılığıyla Gremlin bağlantısı yapılır. WebSocket bağlantılarında gerçek zamanlı bir zaman yoksa, Azure Cosmos DB Gremlin API, boşta bağlantıları 30 dakika sonra işlem yapılmadan sonlandırır.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Yerel Gremlin sürücülerinde neden Fluent API çağrısı kullanmıyorum?

Akıcı API çağrıları henüz Azure Cosmos DB Gremlin API 'SI tarafından desteklenmiyor. Akıcı API çağrıları, şu anda Azure Cosmos DB Gremlin API tarafından desteklenmeyen, bytecode desteği olarak bilinen bir iç biçimlendirme özelliği gerektirir. Aynı nedenden dolayı, en son Gremlin-JavaScript sürücüsü de şu anda desteklenmemektedir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB Gremlin tel protokol desteği](gremlin-support.md)
* [Gremlin konsolunu](create-graph-gremlin-console.md) kullanarak Azure Cosmos DB Graph veritabanı oluşturma, sorgulama ve çapraz geçiş yapma
