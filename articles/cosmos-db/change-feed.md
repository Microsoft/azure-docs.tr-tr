---
title: Azure Cosmos DB değişiklik akışı desteğiyle çalışma
description: Belgelerdeki değişiklikleri izlemek ve Tetikleyiciler ve analiz sistemlerini güncel tutmak gibi olay tabanlı işleme gerçekleştirmek için Azure Cosmos DB akış desteğini değiştirin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 8e6bd3dadd636127f212db0ea0c0755a6b52a087
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757018"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Azure Cosmos DB akışı değiştirme-genel bakış

Azure Cosmos DB akış desteğini değiştirme, tüm değişiklikler için bir Azure Cosmos kapsayıcısını dinleyerek işe yarar. Ardından çıkış olarak, değiştirilen belgelerin değiştirilme zamanına göre sıralandığı bir belge listesi oluşturur. Değişiklikler kalıcı hale getirilir, zaman uyumsuz ve artırımlı olarak işlenebilir ve çıkış, paralel işleme için bir veya daha fazla tüketiciye dağıtılabilir. 

Azure Cosmos DB IoT, oyun, perakende ve işlemsel günlük uygulamalar için uygundur. Bu uygulamalardaki ortak bir tasarım deseninin ek eylemleri tetiklemek için verilerde yapılan değişiklikler kullanılır. Ek eylemlere örnek olarak şunlar verilebilir:

* Bir öğe eklendiğinde veya güncelleştirilirken bir bildirim veya bir API çağrısı tetikleniyor.
* İşletimsel veriler üzerinde IoT veya gerçek zamanlı analiz işleme için gerçek zamanlı akış işleme.
* Bir önbellek veya arama altyapısı ya da bir veri ambarı ile eşitleyerek veya verileri soğuk depolamaya arşivleyerek ek veri hareketleri.

Azure Cosmos DB değişiklik akışı, aşağıdaki görüntüde gösterildiği gibi, bu desenlerden her biri için etkili ve ölçeklenebilir çözümler oluşturmanıza olanak sağlar:

![Gerçek zamanlı analiz ve olay odaklı bilgi işlem senaryolarına Azure Cosmos DB değişiklik akışını kullanma](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Desteklenen API 'Ler ve istemci SDK 'Ları

Bu özellik şu anda aşağıdaki Azure Cosmos DB API 'Leri ve istemci SDK 'Ları tarafından desteklenmektedir.

| **İstemci sürücüleri** | **Azure CLI** | **SQL APı 'SI** | **Cassandra API** | **MongoDB için Azure Cosmos DB API 'SI** | **Gremlin API 'SI**|**Tablo API’si** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Yok | Yes | Hayır | Hayır | Yes | Hayır |
|Java|Yok|Yes|Hayır|Hayır|Yes|Hayır|
|Python|Yok|Yes|Hayır|Hayır|Yes|Hayır|
|Node/JS|Yok|Yes|Hayır|Hayır|Yes|Hayır|

## <a name="change-feed-and-different-operations"></a>Akışı ve farklı işlemleri değiştirme

Bugün, değişiklik akışında tüm işlemleri görürsünüz. Değişiklik akışını denetleyebileceğiniz işlevler, yalnızca güncelleştirmeler ve eklemeler gibi belirli işlemler için henüz kullanılamaz. Değişiklik akışındaki öğeleri işlerken bu öğeye bağlı olarak, güncelleştirmeler ve filtre için öğe üzerinde "geçici işaretleyici" ekleyebilirsiniz. Şu anda değişiklik akışında silme günlüğü yok. Önceki örneğe benzer şekilde, silinmekte olan öğelerde bir yumuşak işaret ekleyebilirsiniz, örneğin, "Deleted" adlı öğeye bir öznitelik ekleyebilir ve bunu "true" olarak ayarlayabilir ve öğe üzerinde bir TTL ayarlarsınız, böylece otomatik olarak silinebilir. Geçmiş öğeler için değişiklik akışını okuyabilir (öğeye karşılık gelen en son değişiklik, ara değişiklikleri içermez), örneğin, beş yıl önce eklenmiş olan öğeler. Öğe silinmemişse, değişiklik akışını kapsayıcının kaynağına kadar okuyabilirsiniz.

### <a name="sort-order-of-items-in-change-feed"></a>Değişiklik akışında öğelerin sıralama düzeni

Değişiklik akışı öğeleri değiştirme zamanının sırasıyla gelir. Bu sıralama düzeni mantıksal bölüm anahtarı başına garanti edilir.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Çok bölgeli Azure Cosmos hesaplarında akışı değiştirme

Çok bölgeli bir Azure Cosmos hesabında, bir yazma bölgesi yük devrettikten sonra değişiklik akışı el ile yük devretme işleminde çalışır ve bitişik olur.

### <a name="change-feed-and-time-to-live-ttl"></a>Akışı ve yaşam süresini (TTL) değiştirme

Bir öğede TTL (yaşam süresi) özelliği ayarlanırsa, değişiklik akışı süresiz olarak kalır. Veriler silinmediği takdirde değişiklik akışında kalır.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Akış ve _etag, _lsn veya _ts 'yi Değiştir

_Etag biçimi iç, her zaman değiştirebildiğinden, buna bağımlılığı almanız gerekir. _ts bir değiştirme veya oluşturma zaman damgasıdır. Kronolojik karşılaştırma için _ts kullanabilirsiniz. _lsn yalnızca değişiklik akışı için eklenen bir toplu iş KIMLIĞIDIR; işlem KIMLIĞINI temsil eder. Birçok öğe aynı _lsn 'ye sahip olabilir. FeedResponse üzerinde ETag, öğede gördüğünüz _etag öğesinden farklı. _etag bir iç tanımlayıcıdır ve eşzamanlılık denetimi, öğenin sürümü hakkında bilgi için kullanılır, ancak ETag, akışı sıralama için kullanılır.

## <a name="change-feed-use-cases-and-scenarios"></a>Akış kullanım örneklerini ve senaryolarını değiştirme

Değişiklik akışı, büyük veri kümelerinin yüksek miktarda yazma hacimiyle verimli bir şekilde işlenmesini sağlar. Değişiklik akışı Ayrıca, nelerin değiştiğini belirlemek için bir veri kümesinin sorgulanmasına bir alternatif sağlar.

### <a name="use-cases"></a>Uygulama alanları

Örneğin, değişiklik akışı ile aşağıdaki görevleri verimli bir şekilde gerçekleştirebilirsiniz:

* Bir önbelleği güncelleştirme, bir arama dizinini güncelleştirme veya bir veri ambarını Azure Cosmos DB depolanan verilerle güncelleştirme.

* Uygulama düzeyinde veri katmanlama ve arşivleme uygulayın, örneğin, [Azure Blob depolama](../storage/common/storage-introduction.md)gibi diğer depolama sistemlerine "soğuk verileri" Azure Cosmos DB ve yaş dışına "sık verileri" depolayın.

* Başka bir Azure Cosmos hesabına veya farklı bir mantıksal bölüm anahtarına sahip başka bir Azure Cosmos kapsayıcısına sıfır-zaman geçişleri gerçekleştirin.

* Azure Cosmos DB gerçek zamanlı, toplu işlem ve sorgu özelliklerini desteklediği Azure Cosmos DB kullanarak [lambda mimarisini](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) uygulayın. bu sayede, düşük TCO ile Lambda mimarisini etkinleştirir.

* Cihazlardan, sensörlerden, altyapıdan ve uygulamalardan olay verilerini alın ve depolayın ve örneğin [Spark](../hdinsight/spark/apache-spark-overview.md)kullanarak bu olayları gerçek zamanlı olarak işleyin.  Aşağıdaki görüntüde, değişiklik akışı aracılığıyla Azure Cosmos DB kullanarak lambda mimarisini nasıl uygulayabileceğinizi gösterilmektedir:

![Alma ve sorgu için Azure Cosmos DB tabanlı lambda işlem hattı](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Senaryolar

Değişiklik akışı ile kolayca uygulayabileceğiniz bazı senaryolar aşağıda verilmiştir:

* [Sunucusuz](https://azure.microsoft.com/solutions/serverless/) Web veya mobil uygulamalarınız içinde, müşterinizin profilinde, tercihlerinde veya konumlarından yapılan tüm değişiklikler gibi olayları izleyebilir ve örneğin Azure kullanarak cihazlarına anında iletme bildirimleri göndererek belirli eylemleri tetikleyebilirsiniz. [ İşlevler](change-feed-functions.md).

* Bir oyun oluşturmak için Azure Cosmos DB kullanıyorsanız, örneğin, tamamlanmış oyunlardan puanları temel alarak gerçek zamanlı öncü panolar uygulamak için değişiklik akışı ' nı kullanabilirsiniz.


## <a name="working-with-change-feed"></a>Değişiklik akışı ile çalışma

Değişiklik akışı ile aşağıdaki seçenekleri kullanarak çalışabilirsiniz:

* [Azure Işlevleri ile değişiklik akışını kullanma](change-feed-functions.md)
* [Değişiklik akışı işlemcisi ile değişiklik akışını kullanma](change-feed-processor.md) 

Değişiklik akışı, kapsayıcı içindeki her mantıksal bölüm anahtarı için kullanılabilir ve aşağıdaki görüntüde gösterildiği gibi paralel işleme için bir veya daha fazla tüketiciye dağıtılabilir.

![Azure Cosmos DB değişiklik akışını dağıtılmış işleme](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Değişiklik akışı özellikleri

* Değişiklik akışı, tüm Azure Cosmos hesapları için varsayılan olarak etkindir.

* Azure Cosmos veritabanınız ile ilişkili bölgelerin herhangi birinde olduğu Azure Cosmos DB gibi, değişiklik akışından okumak için [sağlanan aktarım hızını](request-units.md) kullanabilirsiniz.

* Değişiklik akışı, kapsayıcı içindeki öğelere yapılan ekleme ve güncelleştirme işlemlerini içerir. Öğelerin (örneğin, belgeler) içinden silme yerine "geçici silme" bayrağını ayarlayarak silmeleri yakalayabilirsiniz. Alternatif olarak, [TTL özelliği](time-to-live.md)olan öğeleriniz için sonlu bir süre sonu süresi ayarlayabilirsiniz. Örneğin, 24 saat ve silmeleri yakalamak için bu özelliğin değerini kullanın. Bu çözümle, değişiklikleri TTL süre sonu süresinden daha kısa bir zaman aralığı içinde işleyebilmeniz gerekir. 

* Bir öğedeki her değişiklik, değişiklik akışında tam olarak bir kez görünür ve istemciler checkişaret mantığını yönetmelidir. Kontrol noktalarını yönetme karmaşıklığına engel olmak istiyorsanız, değişiklik akışı işlemcisi otomatik onay işareti ve "en az bir kez" semantiği sağlar. Bkz. [değişiklik akışı işlemcisi ile değişiklik akışını kullanma](change-feed-processor.md).

* Değişiklik günlüğüne yalnızca belirli bir öğe için en son değişiklik eklenir. Ara değişiklikler kullanılamıyor olabilir.

* Değişiklik akışı, her mantıksal bölüm anahtarı değeri içindeki değiştirilme sırasına göre sıralanır. Bölüm anahtarı değerleri arasında garantili bir sıra yok.

* Değişiklikler herhangi bir noktadan eşitlenebilir, bu, değişikliklerin kullanılabildiği sabit veri saklama süresi değildir.

* Değişiklikler, bir Azure Cosmos kapsayıcısının tüm mantıksal bölüm anahtarları için paralel olarak kullanılabilir. Bu yetenek, büyük kapsayıcılardaki değişikliklerin birden çok tüketici tarafından paralel olarak işlenmesine izin verir.

* Uygulamalar aynı kapsayıcıda aynı anda birden fazla değişiklik akışı talep edebilir. Changefeedoçen. StartTime, ilk başlangıç noktası sağlamak için kullanılabilir. Örneğin, belirli bir saat saatine karşılık gelen devamlılık belirtecini bulmak için. Belirtilmişse, StartTime ve Startfromstarted değerleri üzerinden WINS. Changefeedo,. StartTime duyarlık değeri ~ 5 saniye. 

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde değişiklik akışı hakkında daha fazla bilgi edinmek için devam edebilirsiniz:

* [Değişiklik akışını okuma seçenekleri](read-change-feed.md)
* [Azure Işlevleri ile değişiklik akışını kullanma](change-feed-functions.md)
* [Değişiklik akışı işlemcisini kullanma](change-feed-processor.md)
