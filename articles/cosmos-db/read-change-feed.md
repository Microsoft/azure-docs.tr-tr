---
title: Azure Cosmos DB değişiklik akışını okuma
description: Bu makalede, Azure Cosmos DB değişiklik akışını okuma ve erişme için kullanabileceğiniz farklı seçenekler açıklanmaktadır.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 7021367e1230573343ddf57ccd399d998ad5280e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339283"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Azure Cosmos DB değişiklik akışını okuma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Anında iletme modeli veya çekme modeli kullanarak Azure Cosmos DB değişiklik akışı ile çalışabilirsiniz. Bir anında iletme modeliyle, değişiklik akışı işlemcisi bu çalışmayı işlemek için iş mantığı olan bir istemciye işi gönderir. Ancak, son işlenen çalışmanın çalışma ve depolama durumunu denetleme karmaşıklığı, değişiklik akışı işlemcisi içinde işlenir.

Bir çekme modeliyle, istemci işi sunucudan çekmeniz gerekir. Bu durumda, yalnızca çalışmayı işlemek için iş mantığı yoktur, ancak son işlenen iş için durumu depolar, birden fazla istemci genelinde yük dengelemeyi işleme, paralel olarak çalışmayı işleme ve hataları işleme.

Azure Cosmos DB değişiklik akışından okurken, endişelenmenize gerek duymayabilmeniz için genellikle bir anında iletme modeli kullanmanızı öneririz:

- Sonraki değişiklikler için değişiklik akışı yoklanıyor.
- Son işlenen değişiklik için durum depolanıyor. Değişiklik akışından okurken, bu otomatik olarak bir [kira kapsayıcısında](change-feed-processor.md#components-of-the-change-feed-processor)depolanır.
- Değişiklikleri kullanan birden çok istemci arasında yük dengeleme. Örneğin, bir istemci, değişiklikleri işlemeye devam edemiyor ve diğeri kullanılabilir kapasiteye sahip olamaz.
- [Hataları işleme](change-feed-processor.md#error-handling). Örneğin, kodda işlenmeyen bir özel durum veya geçici ağ sorunu sonrasında doğru şekilde işlenen başarısız değişiklikleri otomatik olarak yeniden deniyor.

Azure Cosmos DB değişiklik akışını kullanan senaryoların çoğu, push model seçeneklerinden birini kullanacaktır. Ancak, çekme modelinin ek düşük düzey denetimini isteyebileceğiniz bazı senaryolar vardır. Bu modüller şunlardır:

- Belirli bir bölüm anahtarından değişiklikler okunuyor
- İstemcinizin işlenmek üzere değişiklikleri alacağı hızların denetlenmesi
- Değişiklik akışında var olan verileri bir kerelik okuma işlemi yapma (örneğin, veri geçişi yapmak için)

## <a name="reading-change-feed-with-a-push-model"></a>Bir anında iletme modeliyle değişiklik akışını okuma

Anında iletme modelinin kullanılması, değişiklik akışından okunması için en kolay yoldur. Bir itme modeliyle değişiklik akışından okuma yapmak için iki yol vardır: [Azure işlevleri Cosmos DB Tetikleyiciler](change-feed-functions.md) ve [değişiklik akışı işlemci kitaplığı](change-feed-processor.md). Azure Işlevleri arka planda değişiklik akışı işlemcisini kullanır, bu nedenle bunlar değişiklik akışını bulmanın çok benzer yollardır. Değişiklik akışını okumaktan tamamen farklı bir şekilde değil, değişiklik akışı işlemcisi için yalnızca bir barındırma platformu olarak Azure Işlevleri düşünün.

### <a name="azure-functions"></a>Azure İşlevleri

Yalnızca değişiklik akışını kullanmaya başladıysanız, Azure Işlevleri en basit seçenektir. Kolaylık nedeniyle, çoğu akış kullanım örnekleri için de önerilen seçenektir. Azure Cosmos DB için bir Azure Işlevleri tetikleyicisi oluşturduğunuzda Bağlanılacak kapsayıcıyı seçersiniz ve kapsayıcıda her değişiklik yapıldığında Azure Işlevi tetiklenir. Azure Işlevleri arka planda değişiklik akışı işlemcisini kullandığından, kapsayıcının [bölümlerinde](partitioning-overview.md)değişiklik işlemeyi otomatik olarak paralelleştirin.

Azure Işlevleri ile geliştirme, kolay bir deneyimdir ve değişiklik akışı işlemcisini kendi kendinize dağıtmaktan daha hızlı olabilir. Tetikleyiciler, Azure Işlevleri Portalı kullanılarak veya SDK 'lar kullanılarak programlı bir şekilde oluşturulabilir. Visual Studio ve VS Code Azure Işlevleri yazmak için destek sağlar ve platformlar arası geliştirme için Azure Işlevleri CLı 'yi de kullanabilirsiniz. Masaüstünüzdeki kodu yazabilir ve hata ayıklamanıza ve sonra işlevi tek bir tıklama ile dağıtmanıza de tıklayabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure işlevleri 'ni kullanarak sunucusuz veritabanı hesaplama](serverless-computing-database.md) ve [Azure işlevleri Ile değişiklik akışını kullanma](change-feed-functions.md) makaleleri.

### <a name="change-feed-processor-library"></a>Akış işlemcisi kitaplığını Değiştir

Değişiklik akışı işlemcisi, değişiklik akışına daha fazla denetim sağlar ve yine de karmaşıklığı gizler. Değişiklik akışı işlemci kitaplığı, işleme işlevinizin kitaplık tarafından çağrıldığı gözlemci düzeniyle uyar. Değişiklik akışı işlemci kitaplığı, değişiklikleri otomatik olarak denetler ve değişiklikler bulunursa istemciye "gönderin". Yüksek verimlilik değişikliği akışınız varsa, değişiklik akışını okumak için birden çok istemci örneği oluşturabilirsiniz. Değişiklik akışı işlemci kitaplığı, yükü farklı istemciler arasında otomatik olarak böler. Yük Dengeleme için birden fazla istemci veya kira durumunu korumak için herhangi bir mantık uygulamak zorunda kalmazsınız.

Değişiklik akışı işlemci kitaplığı, tüm değişikliklerin "en az bir kez" teslimini garanti eder. Diğer bir deyişle, değişiklik akışı işlemci kitaplığını kullanıyorsanız, değişiklik akışındaki her öğe için işleme işleviniz başarıyla çağırılır. İşlem işlevinizdeki iş mantığındaki işlenmeyen bir özel durum varsa, başarısız değişiklikler başarıyla işlenene kadar yeniden denenir. Değişiklik akışı işlemcinizin "takılmış" olarak aynı değişiklikleri denemesini engellemek için, işlem işlevinizde, özel durum üzerine, bir teslim edilemeyen ileti kuyruğuna bir mantık ekleyin. [Hata işleme](change-feed-processor.md#error-handling)hakkında daha fazla bilgi edinin.

Azure Işlevleri 'nde hata işleme önerisi aynıdır. Belge, özel durum üzerine, bir atılacak iletiler kuyruğuna yazmak için temsilci kodunuzda Logic de eklemeniz gerekir. Ancak, Azure işlevinizde işlenmeyen bir özel durum varsa, özel durumu oluşturan değişiklik otomatik olarak yeniden denenmez. İş mantığındaki işlenmeyen bir özel durum varsa, Azure Işlevi bir sonraki değişikliği işlemeye geçer. Azure Işlevi, başarısız olan değişikliği yeniden denemeyecek.

Azure Işlevleri gibi, değişiklik akışı işlemci kitaplığı ile geliştirme kolaydır. Ancak, değişiklik akışı işlemcisi için bir veya daha fazla konağın dağıtılmasından siz sorumlusunuz. Ana bilgisayar, değişiklikleri dinlemek için akış işlemcisini Değiştir ' i kullanan bir uygulama örneğidir. Azure Işlevleri otomatik ölçeklendirme özelliklerine sahip olsa da, konaklarınızı ölçeklendirmekten siz sorumlusunuz. Daha fazla bilgi için bkz. [değişiklik akışı işlemcisini kullanma](change-feed-processor.md#dynamic-scaling). Değişiklik akışı işlemci kitaplığı, [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)'nin bir parçasıdır.

## <a name="reading-change-feed-with-a-pull-model"></a>Çekme modeliyle değişiklik akışını okuma

[Değişiklik akışı çekme modeli](change-feed-pull-model.md) , değişiklik akışını kendi hızınızda kullanmanıza olanak sağlar. Değişikliklerin istemci tarafından istenmesi ve değişiklikler için otomatik yoklama olmaması gerekir. Son işlenen değişikliği (gönderim modelinin kira kapsayıcısına benzer şekilde) kalıcı olarak "yer işareti" koymak istiyorsanız, [bir devamlılık belirteci kaydetmeniz](change-feed-pull-model.md#saving-continuation-tokens)gerekir.

Değişiklik akışı çekme modelini kullanarak, değişiklik akışında daha düşük düzey denetim edinirsiniz. Çekme modeliyle değişiklik akışını okurken üç seçeneğiniz vardır:

- Tüm kapsayıcı için değişiklikleri oku
- Belirli bir [Feedrange](change-feed-pull-model.md#using-feedrange-for-parallelization) için değişiklikleri oku
- Belirli bir bölüm anahtarı değeri için okuma değişiklikleri

Değişiklik akışı işlemcisiyle yaptığınız gibi, değişikliklerin birden çok istemci arasında işlenmesini paralel hale getirmek sağlayabilirsiniz. Ancak çekme modeli, istemciler arasında yük dengelemeyi otomatik olarak işlemez. Değişiklik akışını paralel hale getirmek için çekme modelini kullandığınızda öncelikle bir FeedRanges listesi elde edersiniz. Bir FeedRange, bölüm anahtarı değerlerini bir aralığa yaymıştır. FeedRanges elde eden ve bunları makineleriniz arasında dağıtan bir Orchestrator sürecinize sahip olmanız gerekir. Daha sonra bu FeedRanges, birden fazla makinenin değişiklik akışını paralel olarak okuyabilmeniz için kullanabilirsiniz.

Çekme modeliyle yerleşik "en az bir kez" teslim garantisi yoktur. Çekme modeli, hataları nasıl işlemek istediğinize karar vermek için düşük düzeyde denetim sağlar.

> [!NOTE]
> Değişiklik akışı çekme modeli, şu anda yalnızca [Azure Cosmos DB .NET SDK sürümünde önizlemededir](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) . Önizleme henüz diğer SDK sürümleri için kullanılamaz.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra ve MongoDB API 'Lerinde akışı değiştirme

Değişiklik akışı işlevselliği, MongoDB API 'sinde değişiklik akışları olarak ve Cassandra API ' de karşılaştırma ile sorgu olarak ortaya çıkmış. MongoDB API 'SI için uygulama ayrıntıları hakkında daha fazla bilgi edinmek için bkz. [MongoDB için Azure Cosmos DB API 'sindeki akışları değiştirme](mongodb-change-streams.md).

Native Apache Cassandra değişiklik verilerini yakalama (CDC), belirli tabloları arşivleme için bayrakla işaretleme ve CDC günlüğü için yapılandırılabilir bir diske ulaşıldığında bu tablolara yazmaları reddetme mekanizması sağlar. Cassandra için Azure Cosmos DB API 'sindeki akış değiştirme özelliği, değişiklikleri CQL aracılığıyla koşullu olarak sorgulama yeteneğini geliştirir. Uygulama ayrıntıları hakkında daha fazla bilgi edinmek için bkz. [Cassandra için Azure Cosmos DB API 'sindeki akışı değiştirme](cassandra-change-feed.md).

## <a name="next-steps"></a>Sonraki adımlar

Şimdi aşağıdaki makalelerde değişiklik akışı hakkında daha fazla bilgi edinebilirsiniz:

* [Değişiklik akışına genel bakış](change-feed.md)
* [Azure Işlevleri ile değişiklik akışını kullanma](change-feed-functions.md)
* [Değişiklik akışı işlemci kitaplığını kullanma](change-feed-processor.md)
