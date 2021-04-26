---
title: Azure Cosmos DB akış tasarımı desenlerini değiştirme
description: Ortak değişiklik akışı tasarım desenlerine genel bakış
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334621"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Azure Cosmos DB akış tasarımı desenlerini değiştirme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB değişiklik akışı, büyük veri kümelerinin yüksek miktarda yazma hacimiyle verimli bir şekilde işlenmesini sağlar. Değişiklik akışı Ayrıca, nelerin değiştiğini belirlemek için bir veri kümesinin sorgulanmasına bir alternatif sağlar. Bu belge, ortak değişiklik akışı tasarım desenleri, tasarım avantajları ve değişiklik akışı sınırlamalarını odaklanır.

Azure Cosmos DB IoT, oyun, perakende ve işlemsel günlük uygulamalar için uygundur. Bu uygulamalardaki ortak bir tasarım deseninin ek eylemleri tetiklemek için verilerde yapılan değişiklikler kullanılır. Ek eylemlere örnek olarak şunlar verilebilir:

* Bir öğe eklendiğinde veya güncelleştirilirken bir bildirim veya bir API çağrısı tetikleniyor.
* İşletimsel veriler üzerinde IoT veya gerçek zamanlı analiz işleme için gerçek zamanlı akış işleme.
* Önbellek, arama altyapısı, veri ambarı veya soğuk depolama ile eşitleme gibi veri taşıma.

Azure Cosmos DB değişiklik akışı, aşağıdaki görüntüde gösterildiği gibi, bu desenlerden her biri için etkili ve ölçeklenebilir çözümler oluşturmanıza olanak sağlar:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Gerçek zamanlı analiz ve olay odaklı bilgi işlem senaryolarına Azure Cosmos DB değişiklik akışını kullanma" border="false":::

## <a name="event-computing-and-notifications"></a>Olay bilgi işlem ve bildirimler

Azure Cosmos DB değişiklik akışı, bir bildirim tetiklenmesi veya belirli bir olaya göre API 'ye çağrı gönderilmesi gereken senaryoları kolaylaştırabilir. Kapsayıcıyı değişiklik için otomatik olarak yoklamak ve her yazma veya güncelleştirme olduğunda bir dış API 'yi çağırmak için, [değişiklik akışı Işlem kitaplığını](change-feed-processor.md) kullanabilirsiniz.

Ayrıca, isteğe bağlı olarak bir bildirim tetikleyip belirli ölçütlere göre bir API çağrısı gönderebilirsiniz. Örneğin, [Azure işlevleri](change-feed-functions.md)'ni kullanarak değişiklik akışından okuyorsanız, yalnızca belirli bir ölçüte uyulduğunda bildirim göndermek için işlevine Logic koyabilirsiniz. Azure Işlev kodu her yazma ve güncelleştirme sırasında yürütülecektir, ancak bildirim yalnızca belirli ölçütler karşılandıysa gönderilir.

## <a name="real-time-stream-processing"></a>Gerçek zamanlı akış işleme

Azure Cosmos DB değişiklik akışı, işlem verilerinde IoT veya gerçek zamanlı analiz işleme için gerçek zamanlı akış işleme için kullanılabilir.
Örneğin, cihazlardan, sensörlerden, altyapıdan ve uygulamalardan olay verileri alıp saklayabilir ve [Spark](../hdinsight/spark/apache-spark-overview.md)kullanarak bu olayları gerçek zamanlı olarak işleyebilirsiniz. Aşağıdaki görüntüde, değişiklik akışı aracılığıyla Azure Cosmos DB kullanarak bir lambda mimarisini nasıl uygulayabileceğinizi gösterilmektedir:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Alma ve sorgu için Azure Cosmos DB tabanlı lambda işlem hattı" border="false":::

Çoğu durumda, akış işleme uygulamaları ilk olarak Azure Event hub veya Apache Kafka gibi geçici bir ileti kuyruğuna yüksek miktarda gelen verileri alır. Değişiklik akışı, Azure Cosmos DB aşırı düşük okuma ve yazma gecikme süresiyle sürekli yüksek oranda veri alımı destekleyebilme nedeniyle harika bir alternatiftir. Azure Cosmos DB değişiklik akışı bir ileti kuyruğu üzerinden avantajları şunları içerir:

### <a name="data-persistence"></a>Veri kalıcılığı

Azure Cosmos DB yazılan veriler değişiklik akışında görünür ve silinene kadar korunur. İleti kuyrukları genellikle maksimum saklama süresine sahiptir. Örneğin, [Azure Olay Hub](https://azure.microsoft.com/services/event-hubs/) 'ı 90 günlük maksimum veri bekletmesi sağlar.

### <a name="querying-ability"></a>Özelliği sorgulama

Cosmos kapsayıcısının değişiklik akışından okumayı ek olarak, Azure Cosmos DB depolanan verilerde SQL sorguları da çalıştırabilirsiniz. Değişiklik akışı, kapsayıcıda zaten bulunan verilerin bir yinelemesi değil, verileri okurken yalnızca farklı bir mekanizma değildir. Bu nedenle, değişiklik akışından veri okuduğunuzda, her zaman aynı Azure Cosmos DB kapsayıcısının sorgularıyla tutarlı olur.

### <a name="high-availability"></a>Yüksek kullanılabilirlik

Azure Cosmos DB,% 99,999 okuma ve yazma kullanılabilirliği sağlar. Birçok ileti kuyruğu farklı olarak, Azure Cosmos DB veriler kolayca küresel olarak dağıtılabilir ve bir [RTO (kurtarma süresi hedefi)](./consistency-levels.md#rto) sıfır ile yapılandırılabilir.

Değişiklik akışındaki öğeleri işledikten sonra, gerçekleştirilmiş bir görünüm oluşturabilir ve toplanmış değerleri Azure Cosmos DB yeniden kalıcı hale getirebilirsiniz. Bir oyun oluşturmak için Azure Cosmos DB kullanıyorsanız, örneğin, tamamlanmış oyunlardan puanları temel alarak gerçek zamanlı öncü panolar uygulamak için değişiklik akışı ' nı kullanabilirsiniz.

## <a name="data-movement"></a>Veri taşıma

Ayrıca, gerçek zamanlı veri taşıma için değişiklik akışından de okuma yapabilirsiniz.

Örneğin, değişiklik akışı aşağıdaki görevleri verimli bir şekilde gerçekleştirmenize yardımcı olur:

* Azure Cosmos DB depolanan verilerle bir önbelleği, arama dizinini veya veri ambarını güncelleştirin.

* Başka bir Azure Cosmos hesabına veya farklı bir mantıksal bölüm anahtarına sahip başka bir Azure Cosmos kapsayıcısına sıfır-zaman geçişleri gerçekleştirin.

* Uygulama düzeyinde veri katmanlaması ve arşivleme uygulayın. Örneğin, [Azure Blob depolama](../storage/common/storage-introduction.md)gibi diğer depolama sistemlerine "soğuk verileri" Azure Cosmos DB ve yaş halinde "etkin verileri" saklayabilirsiniz.

[Verileri bölümler ve kapsayıcılar arasında normalleştirmeye](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)ihtiyacınız olduğunda, bu veri çoğaltması için bir kaynak olarak kapsayıcının değişiklik akışından okuma yapabilirsiniz. Değişiklik akışı ile gerçek zamanlı veri çoğaltma yalnızca nihai tutarlılığı garanti edebilir. Değişiklik akışı Işlemcisinin, Cosmos kapsayıcıınızdaki değişiklikleri işlerken ne [kadar gerisinde olduğunu izleyebilirsiniz](how-to-use-change-feed-estimator.md) .

## <a name="event-sourcing"></a>Olay kaynağını belirleme

Olay kaynağını belirleme [deseninin](/azure/architecture/patterns/event-sourcing) , bu verilere yönelik tam işlem serisini kaydetmek için yalnızca bir Append deposu kullanılması gerekir. Azure Cosmos DB değişiklik akışı, tüm veri alımı yazma (güncelleştirme veya silme yok) olarak modellendiği olay kaynağı oluşturma mimarilerinde merkezi bir veri deposu olarak harika bir seçenektir. Bu durumda, Azure Cosmos DB her yazma bir "olay" ve değişiklik akışında geçmiş olayların tam bir kaydına sahip olacaksınız. Merkezi olay deposu tarafından yayımlanan olayların tipik kullanımları, gerçekleştirilmiş görünümleri sürdürmek veya dış sistemlerle tümleştirme içindir. Değişiklik akışında bekletme için zaman sınırı olmadığından, tüm geçmiş olayları Cosmos kapsayıcısının değişiklik akışından başlayarak okuyarak yeniden çalabilirsiniz.

[Birden çok değişiklik akışı tüketicilerinin aynı kapsayıcının değişiklik akışına abone](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)olmasını sağlayabilirsiniz. [Kira kapsayıcısının](change-feed-processor.md#components-of-the-change-feed-processor) sağlanan verimlilik dışında, değişiklik akışını kullanma maliyeti yoktur. Değişiklik akışı, kullanılıp kullanılmadığından bağımsız olarak her kapsayıcıda kullanılabilir.

Azure Cosmos DB, yatay ölçeklenebilirlik ve yüksek kullanılabilirliğe sahip olduğu için, olay kaynağını belirleme düzeninde bulunan harika bir ön ek, kalıcı veri deposudur. Ayrıca, değişiklik akışı Işlemci Kitaplığı ["en az bir kez](change-feed-processor.md#error-handling) " garantisi sunarak tüm olayları işlemeyi kaçırmamasını sağlar.

## <a name="current-limitations"></a>Geçerli sınırlamalar

Değişiklik akışında anlamanız gereken önemli sınırlamalar vardır. Bir Cosmos kapsayıcısındaki öğeler her zaman değişiklik akışında kalacağından, değişiklik akışı tam bir işlem günlüğü değildir. Değişiklik akışını kullanan bir uygulama tasarlarken göz önünde bulundurmanız gereken önemli alanların vardır.

### <a name="intermediate-updates"></a>Ara güncelleştirmeler

Değişiklik akışına yalnızca belirli bir öğe için en son değişiklik eklenir. Değişiklikleri işlerken, en son kullanılabilir öğe sürümünü okuyacaksınız. Aynı öğeye kısa bir süre içinde birden çok güncelleştirme varsa, ara güncelleştirmeleri işlemeyi kaçırmak mümkündür. Güncelleştirmeleri izlemek ve geçmişteki güncelleştirmeleri bir öğeye yeniden yürütebilmek istiyorsanız, bu güncelleştirmelerin yerine bir dizi yazma olarak modellemeyi öneririz.

### <a name="deletes"></a>/Delete

Değişiklik akışı silmeleri yakalamaz. Kapsayıcıdan bir öğe silerseniz, değişiklik akışından de kaldırılır. Bunu işlemenin en yaygın yöntemi, silinmekte olan öğelere geçici bir işaret eklemektir. "Deleted" adlı bir özellik ekleyebilir ve silme sırasında bunu "true" olarak ayarlayabilirsiniz. Bu belge güncelleştirme, değişiklik akışında görünür. Bu öğe için bir TTL 'yi daha sonra otomatik olarak silinebilecek şekilde ayarlayabilirsiniz.

### <a name="guaranteed-order"></a>Garantili sıra

Bölüm anahtarı değerindeki değişiklik akışında, bölüm anahtarı değerlerinde garanti edilen bir sıra vardır. Size anlamlı bir sıra garantisi veren bir bölüm anahtarı seçmelisiniz.

Örneğin, olay kaynağını belirleme tasarım düzenini kullanarak bir perakende uygulaması düşünün. Bu uygulamada, farklı Kullanıcı eylemleri, Azure Cosmos DB yazma olarak modellenen her bir "olaylardır". Aşağıdaki sırada bazı örnek olaylar oluştuysa düşünün:

1. Müşteri, satın alma sepetine bir öğe ekler
2. Müşteri, alışveriş sepetine B öğesini ekler
3. Müşteri, alışveriş sepetinden öğe A 'yi kaldırır
4. Müşteri teslim alma ve alışveriş sepeti içerikleri sevk edildi

Her müşteri için geçerli alışveriş sepeti içeriklerinin gerçekleştirilmiş bir görünümü tutulur. Bu uygulama, bu olayların oluşma sırasına göre işlenmesini sağlamalıdır. Örneğin, sepet kullanıma alma işlemi, öğe A 'nın kaldırılmadan önce işlenecekse, istenen B öğesine karşılık olarak müşterinin öğesi bir sevk edilmiş olması olabilir. Bu dört olayın, oluşumlarında işlendiklerinden emin olmak için, aynı bölüm anahtarı değeri içinde yer almalıdır. Bölüm anahtarı olarak **Kullanıcı adı** ' nı (her müşterinin benzersiz bir Kullanıcı adı varsa) seçerseniz, bu olayların değişiklik akışında Azure Cosmos DB için yazıldığı sırayla gösterilmesini sağlayabilirsiniz.

## <a name="examples"></a>Örnekler

Aşağıda, Microsoft docs 'ta sağlanan örneklerin kapsamını genişleten bazı gerçek dünyada değişiklik akışı kodu örnekleri verilmiştir:

- [Değişiklik akışına giriş](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Değişiklik akışı etrafında oluşan IoT kullanım örneği](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Değişiklik akışı etrafında ortalanmış perakende kullanım örneği](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Sonraki adımlar

* [Değişiklik akışına genel bakış](change-feed.md)
* [Değişiklik akışını okuma seçenekleri](read-change-feed.md)
* [Azure Işlevleri ile değişiklik akışını kullanma](change-feed-functions.md)