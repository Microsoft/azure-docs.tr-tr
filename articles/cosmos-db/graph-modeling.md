---
title: Azure Cosmos DB Gremlin API 'SI için grafik veri modelleme
description: Cosmos DB Gremlin API kullanarak grafik veritabanını modelleyeceğinizi öğrenin.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 94df90db4a715d2540dfc5ec0aa521d76d22f757
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624222"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API 'SI için grafik veri modelleme

Aşağıdaki belge, grafik veri modelleme önerileri sağlamak için tasarlanmıştır. Bu adım, veri geliştikçe grafik veritabanı sisteminin ölçeklenebilirliğini ve performansını güvence altına almak için önemlidir. Verimli bir veri modeli büyük ölçekli grafiklerle özellikle önemlidir.

## <a name="requirements"></a>Gereksinimler

Bu kılavuzda özetlenen işlem aşağıdaki varsayımlar temelinde olur:
 * Sorun-alanı içindeki **varlıklar** tanımlanır. Bu varlıkların her istek için otomatik olarak tüketilmesi amaçlanmıştır. Diğer bir deyişle, veritabanı sistemi birden çok sorgu isteğinde tek bir varlığın verilerini almak üzere tasarlanmamıştır.
 * Veritabanı sistemi için **okuma ve yazma gereksinimlerinin** anlaşılmasıdır. Bu gereksinimler, grafik veri modeli için gereken iyileştirmelere kılavuzluk eder.
 * [Apache Tinkerpop özelliği grafik standardının](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) ilkeleri iyi anlaşılmıştır.

## <a name="when-do-i-need-a-graph-database"></a>Grafik veritabanına ne zaman ihtiyacım var?

Bir veri etki alanındaki varlıklar ve ilişkilerin aşağıdaki özelliklerden herhangi biri varsa, grafik veritabanı çözümü en iyi şekilde uygulanabilir: 

* Varlıklar, açıklayıcı ilişkiler aracılığıyla **yüksek oranda bağlanır** . Bu senaryodaki avantaj, ilişkilerin depolamada kalıcı hale getirilir.
* **Döngüsel ilişkiler** veya **kendine Başvurulmuş varlıklar**vardır. Bu model genellikle ilişkisel veya belge veritabanlarını kullanırken zorluk sergilemektir.
* Varlıklar arasında **dinamik olarak gelişen ilişkiler** vardır. Bu model özellikle çok sayıda düzeyi olan hiyerarşik veya ağaç biçimli veriler için geçerlidir.
* Varlıklar arasında **çok-çok ilişkisi** vardır.
* **Hem varlıklarda hem de ilişkilerde yazma ve okuma gereksinimleri**vardır. 

Yukarıdaki kriterler karşılandıysanız, bir grafik veritabanı yaklaşımının **sorgu karmaşıklığı**, **veri modeli ölçeklenebilirliği**ve **sorgu performansı**için avantaj sağlaması olasıdır.

Bir sonraki adım, grafiğin analitik veya işlemsel amaçlar için kullanılacağını belirlemektir. Grafik ağır hesaplama ve veri işleme iş yükleri için kullanılmak üzere tasarlanıyorsa, [Cosmos DB Spark bağlayıcısını](https://docs.microsoft.com/azure/cosmos-db/spark-connector) ve [GraphX kitaplığının](https://spark.apache.org/graphx/)kullanımını keşfetmeye değer de vardır. 

## <a name="how-to-use-graph-objects"></a>Graph nesnelerini kullanma

[Apache Tinkerpop özelliği grafik standardı](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) iki tür nesne **köşeleri** ve **kenarlarını**tanımlar. 

Grafik nesnelerindeki özellikler için en iyi uygulamalar şunlardır:

| Object | Özellik | Type | Notlar |
| --- | --- | --- |  --- |
| Köşe | id | Dize | Bölüm başına benzersiz olarak uygulandı. Ekleme sırasında bir değer sağlanmazsa, otomatik üretilen GUID depolanır. |
| Köşe | label | Dize | Bu özellik, köşeyi temsil eden varlık türünü tanımlamak için kullanılır. Değer sağlanmazsa, varsayılan "köşe" değeri kullanılacaktır. |
| Köşe | properties | Dize, Boolean, sayısal | Her köşede anahtar-değer çiftleri olarak depolanan ayrı özelliklerin listesi. |
| Köşe | bölüm anahtarı | Dize, Boolean, sayısal | Bu özellik, köşe ve giden kenarlarının depolanacağı yeri tanımlar. [Grafik bölümlendirme](graph-partitioning.md)hakkında daha fazla bilgi edinin. |
| EDGE | id | Dize | Bölüm başına benzersiz olarak uygulandı. Varsayılan olarak otomatik oluşturulur. Kenarlar genellikle bir KIMLIK tarafından benzersiz bir şekilde alınması gereksinimini içermez. |
| EDGE | label | Dize | Bu özellik, iki köşelerin sahip olduğu ilişkinin türünü tanımlamak için kullanılır. |
| EDGE | properties | Dize, Boolean, sayısal | Her bir kenarda anahtar-değer çiftleri olarak depolanan ayrı özelliklerin listesi. |

> [!NOTE]
> Kenarları, kendi değeri kaynak köşelerine göre otomatik olarak atandığından bölüm anahtarı değeri gerektirmez. [Grafik bölümlendirme](graph-partitioning.md) makalesinde daha fazla bilgi edinin.

## <a name="entity-and-relationship-modeling-guidelines"></a>Varlık ve ilişki modelleme yönergeleri

Aşağıda, bir Azure Cosmos DB Gremlin API grafik veritabanı için veri modellemeye yaklaşıma yönelik bir dizi kılavuz verilmiştir. Bu yönergeler, bir veri etki alanının ve sorgu sorgularının var olan bir tanımı olduğunu varsayar.

> [!NOTE]
> Aşağıda özetlenen adımlar öneriler olarak sunulmuştur. Son model, üretime hazırlanma olarak değerlendirilmeden önce değerlendirilmeli ve test edilmelidir. Ayrıca, aşağıdaki öneriler Azure Cosmos DB Gremlin API uygulamasına özgüdür. 

### <a name="modeling-vertices-and-properties"></a>Modelleme köşeleri ve özellikleri 

Grafik veri modeli için ilk adım, tanımlanan her varlığı bir **köşe nesnesine**eşlemenize yöneliktir. Tüm varlıkların köşelere eşlenmesinin bir ilk adımı ve değişikliğe tabi olması gerekir.

Bir ortak giriş, tek bir varlığın özelliklerini ayrı köşeler olarak eşmaktır. Aşağıdaki örneği, aynı varlığın iki farklı şekilde temsil edildiği yerde göz önünde bulundurun:

* **Köşe tabanlı özellikler**: Bu yaklaşımda varlık, özelliklerini anlatmak için üç ayrı köşe ve iki kenar kullanır. Bu yaklaşım artıklığı azaltada, model karmaşıklığını artırır. Model karmaşıklığının artışı, eklenen gecikme süresi, sorgu karmaşıklığı ve hesaplama maliyetine neden olabilir. Bu model, Bölümlemede zorluk de sunabilir.

![Özellikler için köşeleri olan varlık modeli.](./media/graph-modeling/graph-modeling-1.png)

* **Özellik katıştırılmış**köşeler: Bu yaklaşım, bir köşe içindeki varlığın tüm özelliklerini göstermek için anahtar-değer çifti listesinden yararlanır. Bu yaklaşım daha basit sorgulara ve daha düşük maliyetli traversals neden olacak şekilde daha az model karmaşıklığı sağlar.

![Özellikler için köşeleri olan varlık modeli.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Yukarıdaki örneklerde, yalnızca varlık özelliklerini bölmek için iki yol arasındaki karşılaştırmayı göstermek üzere basitleştirilmiş bir grafik modeli gösterilmektedir.

**Özelliği gömülü** köşeler, genellikle daha performanslı ve ölçeklenebilir bir yaklaşım sağlar. Yeni bir grafik veri modeli için varsayılan yaklaşım bu düzene doğru olmalıdır.

Ancak, bir özelliğe başvuruda bulunan senaryolar avantaj sağlayabilir. Örneğin: başvurulan özellik sık sık güncellendiyse. Sürekli olarak değiştirilen bir özelliği göstermek için ayrı bir köşe kullanmak, güncelleştirmenin gerektirdiği yazma işlemleri miktarını en aza indirir.

### <a name="relationship-modeling-with-edge-directions"></a>Kenar yönlerine sahip ilişki modelleme

Köşeler modellendikten sonra aralarındaki ilişkileri göstermek için kenarlar eklenebilir. Değerlendirilmesi gereken ilk boyut **ilişkinin yönüdür**. 

Edge nesnelerinin, `out()` veya `outE()` işlevi kullanılırken bir çapraz geçiş tarafından izlenen varsayılan bir yönü vardır. Tüm köşeler giden kenarlarıyla depolandığından, bu doğal yönün kullanılması verimli bir işlem ile sonuçlanır. 

Ancak, `in()` işlevi kullanılarak bir kenarın ters yönünde geçiş yapmak, her zaman bir çapraz bölüm sorgusuna neden olur. [Grafik bölümlendirme](graph-partitioning.md)hakkında daha fazla bilgi edinin. `in()` İşlevi kullanarak sürekli geçiş yapmanız gerekiyorsa, her iki yönde de kenar eklenmesi önerilir.

Gremlin adımında `.to()` veya `.from()` koşullarını kullanarak kenar yönünü belirleyebilirsiniz. `.addE()` Ya da [Gremlin API için toplu yürütücü kitaplığı](bulk-executor-graph-dotnet.md)'nı kullanarak.

> [!NOTE]
> Edge nesnelerinin varsayılan olarak bir yönü vardır.

### <a name="relationship-labeling"></a>İlişki etiketleme

Açıklayıcı ilişki etiketlerinin kullanılması, sınır çözümleme işlemlerinin verimliliğini artırır. Bu model aşağıdaki yollarla uygulanabilir:
* Bir ilişkiyi etiketlemek için genel olmayan terimleri kullanın.
* Kaynak köşesinin etiketini, ilişki adı ile hedef köşe etiketi ile ilişkilendirin.

![İlişki etiketleme örnekleri.](./media/graph-modeling/graph-modeling-3.png)

Traverser 'ın kenarları filtrelemesini sağlamak için kullanacağı etiket daha iyidir. Bu karar, sorgu maliyetinde önemli bir etkiye sahip olabilir. Sorgu maliyetini, her zaman [executionProfile adımını kullanarak](graph-execution-profile.md)değerlendirebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar: 
* Desteklenen [Gremlin adımlarının](gremlin-support.md)listesine göz atın.
* Büyük ölçekli grafiklerle başa çıkmak için [grafik veritabanı bölümlendirme](graph-partitioning.md) hakkında bilgi edinin.
* [Yürütme profili adımını](graph-execution-profile.md)kullanarak Gremlin sorgularınızı değerlendirin.
