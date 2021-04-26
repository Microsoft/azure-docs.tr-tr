---
title: Azure Cosmos DB Gremlin API 'sindeki veri bölümleme
description: Azure Cosmos DB ' de bölümlenmiş bir grafiği nasıl kullanabileceğinizi öğrenin. Bu makalede ayrıca bölümlenmiş bir grafik için gereksinimler ve en iyi uygulamalar açıklanmaktadır.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4d76bdcb385ed2fe4b8a697f24187b8e3d5addbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988751"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Azure Cosmos DB'de bölümlenmiş graf kullanma
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB Gremlin API 'sinin temel özelliklerinden biri, büyük ölçekli grafikleri yatay ölçeklendirmeyle işleyebilme olanağıdır. Kapsayıcılar depolama ve aktarım hızı bakımından bağımsız olarak ölçeklendirebilir. Azure Cosmos DB, bir grafik verilerini depolamak için otomatik olarak ölçeklenebilen kapsayıcılar oluşturabilirsiniz. Veriler, belirtilen **bölüm anahtarına** göre otomatik olarak dengelenir.

Kapsayıcının boyutunun 20 GB 'den fazlasını depolaması bekleniyorsa veya saniyede 10.000 ' den fazla istek birimi (ru) ayırmak istiyorsanız bölümlendirme, dahili olarak yapılır. Veriler, belirttiğiniz bölüm anahtarına göre otomatik olarak bölümlenir. Azure portal veya Gremlin sürücülerinden oluşan 3. x veya daha yüksek sürümlerden Grafik kapsayıcıları oluşturursanız bölüm anahtarı gereklidir. 2. x veya Gremlin sürücülerin daha düşük sürümlerini kullanıyorsanız bölüm anahtarı gerekli değildir.

[Azure Cosmos DB bölümleme mekanizmasından](partitioning-overview.md) aynı genel ilkeler aşağıda açıklanan bazı grafiğe özgü iyileştirmeler ile uygulanır.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Grafik bölümleme." border="false":::

## <a name="graph-partitioning-mechanism"></a>Grafik bölümleme mekanizması

Aşağıdaki kılavuzlar Azure Cosmos DB ' de bölümleme stratejisinin nasıl çalıştığını açıklamaktadır:

- **Her iki köşe ve kenar da JSON belgeleri olarak depolanır**.

- Köşeler **bir bölüm anahtarı gerektirir**. Bu anahtar, bir karma algoritma aracılığıyla köşenin depolanacağı bölümü saptacaktır. Bölüm anahtarı özellik adı, yeni bir kapsayıcı oluşturulurken tanımlanır ve şu biçimdedir: `/partitioning-key-name` .

- **Kenarlar, kaynak köşelerine sahip olacak**. Diğer bir deyişle, her bir köşe için bölüm anahtarı, giden kenarları ile birlikte nerede depolandığını tanımlar. Bu iyileştirme, `out()` grafik sorgularında kardinalite kullanılırken çapraz bölüm sorgularını önlemek için yapılır.

- **Kenarlar işaret ettikleri köşelerin başvurularını içerir**. Tüm kenarlar, işaret ettikleri köşelerin bölüm anahtarları ve kimlikleriyle birlikte depolanır. Bu hesaplama, tüm `out()` Yön sorgularının her zaman kapsamlı bölümlenmiş bir sorgu olmasını sağlar ve bu durum, geçici bir çapraz bölümlü sorgu değildir.

- **Grafik sorgularının bir bölüm anahtarı belirtmesi gerekir**. Azure Cosmos DB yatay bölümlemeden tam olarak yararlanmak için, tek bir köşe seçildiğinde, mümkün olduğunda bölüm anahtarı belirtilmelidir. Bölümlenmiş bir grafikte bir veya birden çok köşe seçmek için sorgular aşağıda verilmiştir:

    - `/id` ve `/label` Gremlin API 'deki bir kapsayıcı için bölüm anahtarı olarak desteklenmez.


    - KIMLIĞE göre bir köşe seçerek ve ardından **`.has()` bölüm anahtarı özelliğini belirtmek için adımını kullanarak**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - **Bölüm anahtarı değeri ve kimliği dahil bir tanımlama grubu belirterek** köşe seçme:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - **Bölüm anahtarı değerleri ve kimlikleri için bir dizi tanımlama dizisi** belirtme:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Kimlikleri olan bir köşe kümesi seçme ve **bölüm anahtarı değerlerinin bir listesini belirtme**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Bir sorgunun başlangıcında **bölüm stratejisi** kullanma ve Gremlin sorgusunun geri kalanının kapsamı için bir bölüm belirtme:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Bölümlenmiş grafik kullanırken en iyi uygulamalar

Sınırsız kapsayıcı içeren bölümlenmiş grafikleri kullanırken performans ve ölçeklenebilirlik sağlamak için aşağıdaki yönergeleri kullanın:

- **Bir köşeyi sorgularken bölüm anahtarı değerini her zaman belirtin**. Bilinen bir bölümden köşe alma, performansı elde etmenin bir yoludur. Kenarlar, hedef köşelerine başvuru KIMLIĞI ve bölüm anahtarı içerdiğinden sonraki tüm bitişik Bitişiklik işlemleri her zaman bir bölüm kapsamına alınır.

- **Mümkün olduğunda kenarları sorgularken giden yönü kullanın**. Yukarıda belirtildiği gibi, kenarlar giden yönde kaynak köşelerine göre saklanır. Bu nedenle, veriler ve sorgular bu Düzenle göz önünde bulundurularak tasarlandıysa, çapraz bölümleme sorgularına daha küçük bir şekilde geçiş şansı en aza indirilir. Aksine `in()` sorgu her zaman pahalı bir fan sorgu olacaktır.

- **Verileri bölümler arasında eşit olarak dağıtan bir bölüm anahtarı seçin**. Bu karar, çözümün veri modeline bağlıdır. [Azure Cosmos DB bölümleyip ölçeklendirerek](partitioning-overview.md)uygun bölüm anahtarı oluşturma hakkında daha fazla bilgi edinin.

- **Bir bölümün sınırları içinde veri almak için sorguları iyileştirin**. En iyi bölümleme stratejisi, sorgulama desenlerine hizalanır. Tek bir bölümden veri alan sorgular mümkün olan en iyi performansı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Ardından, aşağıdaki makaleleri okumak için devam edebilirsiniz:

* Azure Cosmos DB bölümünde [bölüm ve ölçek](partitioning-overview.md)hakkında bilgi edinin.
* [GREMLIN API 'de Gremlin desteği](gremlin-support.md)hakkında bilgi edinin.
* [Gremlin API 'Sine giriş](graph-introduction.md)hakkında bilgi edinin.
