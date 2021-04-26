---
title: Azure Cosmos DB değişiklik akışı desteğiyle çalışma
description: Belgelerdeki değişiklikleri izlemek için Azure Cosmos DB akış desteğini değiştirin, tetikleyiciler gibi olay tabanlı işleme ve önbellekleri ve analitik sistemleri güncel tutun
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: c6856a0cb70123f1a3570b611c81660a592fdc1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027806"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Azure Cosmos DB'deki değişiklik akışı
[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

Azure Cosmos DB akış değişikliği, bir kapsayıcıdaki değişikliklerin meydana gelen kalıcı bir kaydıdır. Azure Cosmos DB’de değişiklik akışı desteği, Azure Cosmos kapsayıcısında yapılan değişiklikleri dinleyerek çalışır. Ardından çıkış olarak, değiştirilen belgelerin değiştirilme zamanına göre sıralandığı bir belge listesi oluşturur. Kalıcı değişiklikler zaman uyumsuz ve artımlı olarak işlenebilir ve çıkış paralel işleme için bir veya daha fazla tüketiciye dağıtılabilir.

[Değişiklik akışı tasarım desenleri](change-feed-design-patterns.md)hakkında daha fazla bilgi edinin.

## <a name="supported-apis-and-client-sdks"></a>Desteklenen API 'Ler ve istemci SDK 'Ları

Bu özellik şu anda aşağıdaki Azure Cosmos DB API 'Leri ve istemci SDK 'Ları tarafından desteklenmektedir.

| **İstemci sürücüleri** | **SQL APı 'SI** | **Cassandra için Azure Cosmos DB API 'SI** | **MongoDB için Azure Cosmos DB API’si** | **Gremlin API**|**Tablo API’si** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Yes | Yes | Yes | Yes | Hayır |
|Java|Yes|Yes|Yes|Yes|Hayır|
|Python|Yes|Yes|Yes|Yes|Hayır|
|Node/JS|Yes|Yes|Yes|Yes|Hayır|

## <a name="change-feed-and-different-operations"></a>Akışı ve farklı işlemleri değiştirme

Bugün, değişiklik akışında tüm eklemeleri ve güncelleştirmeleri görürsünüz. Belirli bir işlem türü için değişiklik akışını filtrelememe. Olası bir alternatif, güncelleştirme için öğede "geçici işaretleyici" eklemek ve değişiklik akışındaki öğeleri işlerken bunlara göre filtre uygulamak.

Şu anda değişiklik akışında silme günlüğü yok. Önceki örneğe benzer şekilde, silinmekte olan öğelere de bir yumuşak işaret ekleyebilirsiniz. Örneğin, "Deleted" adlı öğeye bir öznitelik ekleyebilir ve bunu "true" olarak ayarlayabilir ve öğe üzerinde bir TTL ayarlayabilirsiniz, böylece otomatik olarak silinebilir. Geçmiş öğeler için değişiklik akışını okuyabilir (öğeye karşılık gelen en son değişiklik, ara değişiklikleri içermez), örneğin, beş yıl önce eklenmiş olan öğeler. Değişiklik akışını kapsayıcının kaynağına kadar geri alabilirsiniz, ancak bir öğe silinirse, değişiklik akışından kaldırılır.

### <a name="sort-order-of-items-in-change-feed"></a>Değişiklik akışında öğelerin sıralama düzeni

Değişiklik akışı öğeleri değiştirme zamanının sırasıyla gelir. Bu sıralama düzeni mantıksal bölüm anahtarı başına garanti edilir.

### <a name="consistency-level"></a>Tutarlılık düzeyi

Değişiklik akışı nihai tutarlılık düzeyinde kullanılırken, sonraki değişiklik akışı okuma işlemleri arasında (bir okuma işleminin son olayı, sonraki ilk olarak görüntülenir) içinde yinelenen olaylar olabilir.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Çok bölgeli Azure Cosmos hesaplarında akışı değiştirme

Çok bölgeli bir Azure Cosmos hesabında, bir yazma bölgesi yük devrettikten sonra değişiklik akışı el ile yük devretme işleminde çalışır ve bitişik olur.

### <a name="change-feed-and-time-to-live-ttl"></a>Akışı ve yaşam süresini (TTL) değiştirme

Bir öğede TTL (yaşam süresi) özelliği ayarlanırsa, değişiklik akışı süresiz olarak kalır. Veriler silinmediği takdirde değişiklik akışında kalır.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Akışı ve _etag _lsn veya _ts değiştirme

_Etag biçimi dahili olur ve herhangi bir zamanda değiştirebileceğinden buna bağımlılığı almanız gerekir. _ts bir değiştirme veya oluşturma zaman damgası. Kronolojik karşılaştırma için _ts kullanabilirsiniz. _lsn yalnızca değişiklik akışı için eklenen bir toplu iş KIMLIĞIDIR; işlem KIMLIĞINI temsil eder. Birçok öğe aynı _lsn sahip olabilir. FeedResponse üzerinde ETag, öğede gördüğünüz _etag farklıdır. _etag bir iç tanımlayıcıdır ve eşzamanlılık denetimi için kullanılır. _Etag özelliği öğenin sürümünü belirtir, ancak ETag özelliği akışın sıralaması için kullanılır.

## <a name="working-with-change-feed"></a>Değişiklik akışı ile çalışma

Değişiklik akışı ile aşağıdaki seçenekleri kullanarak çalışabilirsiniz:

* [Azure Işlevleri ile değişiklik akışını kullanma](change-feed-functions.md)
* [Değişiklik akışı işlemcisi ile değişiklik akışını kullanma](change-feed-processor.md) 

Değişiklik akışı, kapsayıcı içindeki her mantıksal bölüm anahtarı için kullanılabilir ve aşağıdaki görüntüde gösterildiği gibi paralel işleme için bir veya daha fazla tüketiciye dağıtılabilir.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Azure Cosmos DB değişiklik akışını dağıtılmış işleme" border="false":::

## <a name="features-of-change-feed"></a>Değişiklik akışı özellikleri

* Değişiklik akışı, tüm Azure Cosmos hesapları için varsayılan olarak etkindir.

* Azure Cosmos veritabanınız ile ilişkili bölgelerin herhangi birinde olduğu Azure Cosmos DB gibi, değişiklik akışından okumak için [sağlanan aktarım hızını](request-units.md) kullanabilirsiniz.

* Değişiklik akışı, kapsayıcı içindeki öğelere yapılan ekleme ve güncelleştirme işlemlerini içerir. Öğelerin (örneğin, belgeler) içinden silme yerine "geçici silme" bayrağını ayarlayarak silmeleri yakalayabilirsiniz. Alternatif olarak, [TTL özelliği](time-to-live.md)olan öğeleriniz için sonlu bir süre sonu süresi ayarlayabilirsiniz. Örneğin, 24 saat ve silmeleri yakalamak için bu özelliğin değerini kullanın. Bu çözümle, değişiklikleri TTL süre sonu süresinden daha kısa bir zaman aralığı içinde işleyebilmeniz gerekir.

* Bir öğedeki her değişiklik, değişiklik akışında tam olarak bir kez görünür ve istemciler checkişaret mantığını yönetmelidir. Kontrol noktalarını yönetme karmaşıklığına engel olmak istiyorsanız, değişiklik akışı işlemcisi otomatik onay işareti ve "en az bir kez" semantiği sağlar. Bkz. [değişiklik akışı işlemcisi ile değişiklik akışını kullanma](change-feed-processor.md).

* Değişiklik günlüğüne yalnızca belirli bir öğe için en son değişiklik eklenir. Ara değişiklikler kullanılamıyor olabilir.

* Değişiklik akışı, her mantıksal bölüm anahtarı değeri içindeki değiştirilme sırasına göre sıralanır. Bölüm anahtarı değerleri arasında garantili bir sıra yok.

* Değişiklikler herhangi bir noktadan eşitlenebilir, bu, değişikliklerin kullanılabildiği sabit veri saklama süresi değildir.

* Değişiklikler, bir Azure Cosmos kapsayıcısının tüm mantıksal bölüm anahtarları için paralel olarak kullanılabilir. Bu yetenek, büyük kapsayıcılardaki değişikliklerin birden çok tüketici tarafından paralel olarak işlenmesine izin verir.

* Uygulamalar aynı kapsayıcıda aynı anda birden fazla değişiklik akışı talep edebilir. Changefeedoçen. StartTime, ilk başlangıç noktası sağlamak için kullanılabilir. Örneğin, belirli bir saat saatine karşılık gelen devamlılık belirtecini bulmak için. Bu, belirtilmişse, StartTime ve Startfromstarted değerlerinin üzerine gelir. Changefeedo,. StartTime duyarlık değeri ~ 5 saniye.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra ve MongoDB API 'Lerinde akışı değiştirme

Değişiklik akışı işlevselliği, MongoDB API 'sinde değişiklik akışı olarak ve Cassandra API ' de koşul ile sorgulama olarak ortaya çıkmış. MongoDB API 'SI için uygulama ayrıntıları hakkında daha fazla bilgi edinmek için bkz. [MongoDB için Azure Cosmos DB API 'sindeki akışları değiştirme](mongodb-change-streams.md).

Native Apache Cassandra değişiklik verilerini yakalama (CDC), belirli tabloları arşivleme için bayrakla işaretleme ve CDC günlüğü için yapılandırılabilir bir diske ulaşıldığında bu tablolara yazmaları reddetme mekanizması sağlar. Cassandra için Azure Cosmos DB API 'sindeki akış değiştirme özelliği, değişiklikleri CQL aracılığıyla koşullu olarak sorgulama yeteneğini geliştirir. Uygulama ayrıntıları hakkında daha fazla bilgi edinmek için bkz. [Cassandra için Azure Cosmos DB API 'sindeki akışı değiştirme](cassandra-change-feed.md).

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde değişiklik akışı hakkında daha fazla bilgi edinmek için devam edebilirsiniz:

* [Değişiklik akışını okuma seçenekleri](read-change-feed.md)
* [Azure Işlevleri ile değişiklik akışını kullanma](change-feed-functions.md)
* [Değişiklik akışı işlemcisini kullanma](change-feed-processor.md)
