---
title: Azure Cosmos DB NoSQL ve ilişkisel veritabanları arasındaki farkları anlama
description: Bu makalede NoSQL ve ilişkisel veritabanları arasındaki farklar numaralandırılır
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93088480"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>NoSQL ve ilişkisel veritabanları arasındaki farkları anlama
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bu makalede, NoSQL veritabanlarının ilişkisel veritabanları üzerinde bazı önemli avantajlarından bazıları numaralandıralınacaktır. NoSQL ile çalışma konusundaki bazı güçlükleri de tartışacağız. Mevcut olan farklı veri depolarına derinlemesine bir bakış için [doğru veri deposunu seçme](/azure/architecture/guide/technology-choices/data-store-overview)makalesindeki makaleye göz atın.

## <a name="high-throughput"></a>Yüksek aktarım hızı

İlişkisel bir veritabanı sisteminin korunmasında en belirgin güçlüklerden biri, en ilişkisel altyapıların katı [ACID semantiğini](https://en.wikipedia.org/wiki/ACID)zorlayabilmesi için kilit ve kilitleme uygulamalarından biridir. Bu yaklaşım, veritabanı içinde tutarlı bir veri durumu sağlama açısından avantajlara sahiptir. Bununla birlikte, eşzamanlılık, gecikme süresi ve kullanılabilirliğine göre ağır bir denge vardır. Bu temel mimari kısıtlamalarından dolayı, yüksek işlem hacmi, verileri el ile parçalamayı ihtiyacını ortaya kaydedebilir. El ile parçalama uygulaması, zaman alan ve sorunsuz bir alıştırma olabilir.

Bu senaryolarda, [Dağıtılmış veritabanları](https://en.wikipedia.org/wiki/Distributed_database) daha ölçeklenebilir bir çözüm sunabilir. Ancak, bakım hala maliyetli ve zaman alıcı bir alıştırma olabilir. Yöneticiler, sistemin dağıtılmış doğasını saydam olmasını sağlamak için ek çalışma yapmak zorunda kalabilir. Ayrıca, veritabanının "bağlantısı kesik" yapısına yönelik olarak da hesap gerektirebilir.

[Azure Cosmos DB](./introduction.md) , dünyanın tüm Azure bölgelerinde dağıtılarak bu zorlukları basitleştirir. Bölüm aralıklarının, uygulamayı uygulamayla birlikte sorunsuzca büyütmek için dinamik olarak alt ayrılabilir, ancak yüksek kullanılabilirliği eşzamanlı olarak sürdürmektedir. Ayrıntılı çok kiracılı ve sıkı bir şekilde denetlenen, bulutta yerel kaynak yönetimi, [astonçılık gecikmesi](./consistency-levels.md#consistency-levels-and-latency) ve öngörülebilir performansı kolaylaştırır. Bölümlendirme tamamen yönetiliyor, bu nedenle yöneticilerin kod yazmak veya bölümleri yönetmesi gerekmez.

İşlem birimleriniz, saniyede binlerce işlem gibi çok büyük düzeylere ulaşıyorsanız, dağıtılmış bir NoSQL veritabanını göz önünde bulundurmanız gerekir. En yüksek verimlilik, bakım kolaylığı ve toplam sahip olma maliyetini azaltmak için Azure Cosmos DB göz önünde bulundurun.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Sunucusundan" border="false":::

## <a name="hierarchical-data"></a>Hiyerarşik veriler

Veritabanındaki işlemlerin birçok üst-alt ilişki içerebildiği önemli sayıda kullanım durumu vardır. Bu ilişkiler zaman içinde önemli ölçüde büyüyebilir ve yönetimi zor olabilir. [Hiyerarşik veritabanlarının](https://en.wikipedia.org/wiki/Hierarchical_database_model) formları 1980s sırasında ortaya çıktı, ancak depolamada inefficiency nedeniyle popüler değildi. Ayrıca, aynı zamanda, yaygın olarak kullanılan tüm temel veritabanı yönetim sistemleri tarafından kullanılan standart bir standart haline geldiğinden, [düzenlenmiş Codd 'nin ilişkisel modeli](https://en.wikipedia.org/wiki/Relational_model) de de kaybedilir.

Ancak, bugün belge stili veritabanlarının popülerliği önemli ölçüde artmıştır. Bu veritabanları hiyerarşik veritabanı paradigmasının yeniden oluşturulması, artık diskte veri depolama maliyetiyle ilgili sorunları ortadan kaldırmak olabilir. Sonuç olarak, bir ilişkisel veritabanında çok karmaşık üst-alt varlık ilişkilerinin sürdürülmesi artık modern belge odaklı yaklaşımlarla karşılaştırıldığında bir kenar yumuşatma olarak düşünülebilir.

[Nesne yönelimli tasarımın](https://en.wikipedia.org/wiki/Object-oriented_design)ortaya çıkması ve ilişkisel modellerle birleştirilirken ortaya çıkan [Impedance uyuşmazlığının](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) yanı sıra, belirli kullanım durumları için ilişkisel veritabanlarında de bir kenar yumuşatma vurgulanıyor. Gizli ancak genellikle önemli bakım maliyetleri sonuç olarak ortaya çıkabilir. Bu, [ORM yaklaşımlarının](https://en.wikipedia.org/wiki/Object-relational_mapping) büyük ölçüde hafifletmek için geliştirilmiştir, ancak nesne odaklı yaklaşımlar sayesinde belge odaklı veritabanları Nonetheless. Bu yaklaşımda, geliştiriciler ORM sürücülerine veya Bespoke dile özgü Oo 'e özgü bir özel [veritabanı altyapısına](https://en.wikipedia.org/wiki/Object_database)kaydedilmesine zorlanmaz. Verileriniz çok sayıda üst-alt ilişkisi ve derinlemesine hiyerarşi düzeyleri içeriyorsa, [SQL API 'si](./introduction.md)gibi bir NoSQL belge Azure Cosmos DB veritabanı kullanmayı düşünmek isteyebilirsiniz.

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Karmaşık ağlar ve ilişkiler

Kendi adı verildiğinde ilişkisel veritabanları, ayrıntılı ve karmaşık ilişkilerin modellenmesi için en iyi çözümü sunar. Bunun nedeni, varlıklar arasındaki ilişkilerin gerçekten ilişkisel bir veritabanında bulunmalarıdır. Sorguları kullanarak eşlemeye izin vermek için, Kartezyen birleşimler gerektiren karmaşık ilişkilerle çalışma zamanında hesaplanmaları gerekir. Sonuç olarak, ilişkiler artdıkça, işlemler hesaplama bakımından katlanarak daha pahalıdır hale gelir. Bazı durumlarda, söz konusu varlıkları yönetmeye çalışan ilişkisel bir veritabanı kullanılamaz hale gelir.

"Ağ" veritabanlarının çeşitli biçimleri, ilişkisel veritabanlarının meydana getirme zamanı sırasında ortaya çıktı, ancak hiyerarşik veritabanlarında olduğu gibi, bu sistemler de popülerliği kazanmaya uğramıştır. Yavaş benimseme, zaman içinde kullanım örneklerinin olmamasından ve depolama verimsizlikleri kaynaklanıyor. Bugün grafik veritabanı motorları, ağ veritabanı paradigmasının bir yeniden ortaya çıkmasına kabul edilebilir. Bu sistemlerle ilgili önemli yarar, ilişkilerin veritabanı içinde "First Class vatandaşları" olarak depolanmasıdır. Bu nedenle, çapraz geçiş ilişkileri, her yeni JOIN veya Cross ürünüyle zaman karmaşıklığını artırmak yerine sabit zamanlı olarak yapılabilir.

Veritabanınızda karmaşık bir ilişki ağı bulundururken, bu verileri yönetmek için [Azure Cosmos DB Gremlin API 'si](./graph-introduction.md) gibi bir grafik veritabanını göz önünde bulundurmanız gerekebilir.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Veritabanı diyagramı, birbirine bağlı çeşitli çalışanları ve departmanları gösterir.":::

Azure Cosmos DB, tüm ana NoSQL model türleri için bir API projeksiyonu sunan çok modelli bir veritabanı hizmetidir; Sütun ailesi, belge, grafik ve anahtar değeri. [Gremlin (grafik)](./gremlin-support.md) ve SQL (çekirdek) belge API 'si katmanları tamamen birlikte kullanılabilir. Bu, programlama düzeyinde farklı modeller arasında geçiş avantajlarına sahiptir. Grafik depoları hem karmaşık ağ traversals hem de aynı depodaki belge kayıtları olarak modellenen işlemler bakımından sorgulanabilir.

## <a name="fluid-schema"></a>Akışkan şeması

İlişkisel veritabanlarının özel bir özelliği, şemaların tasarım zamanında tanımlanması gerekir. Bu, bilgi tutarlılığı ve verilerin uygunluk açısından avantajlara sahiptir. Ancak, uygulama büyüdükçe da kısıtlayıcı olabilir. Aynı tabloyu veya veritabanı tanımını paylaşan mantıksal olarak ayrı modeller genelinde şemadaki değişikliklere yanıt verme zaman içinde karmaşık olabilir. Bu tür kullanım örnekleri genellikle uygulama için bir kayıt esasına göre yönetilmesi gereken şemadan faydalanır. Bu, veritabanının "şema belirsiz" olmasını gerektirir ve kayıtların içerdikleri veriler bakımından "kendi kendine açıklama" olmasını sağlar.

Yapıları sürekli olarak yüksek bir hızda değişen verileri yönetiyorsanız, özellikle işlemler veritabanı genelinde uygunluk özelliğinin zorlanmasını zorlaştırıyorsa dış kaynaklardan geliyorsa, Azure Cosmos DB gibi yönetilen bir NoSQL veritabanı hizmetini kullanarak daha şemaya dayalı bir yaklaşım düşünmek isteyebilirsiniz.

## <a name="microservices"></a>Mikro hizmetler

[Mikro hizmetler](https://en.wikipedia.org/wiki/Microservices) stili, son yıllarda önemli ölçüde artmıştır. Bu düzenin, [hizmet yönelimli mimaride](https://en.wikipedia.org/wiki/Service-oriented_architecture)kökleri vardır. Bu modern mikro hizmet mimarilerinde veri iletimi için de standart olarak, belge odaklı NoSQL veritabanlarının büyük çoğunluğunun depolama ortamı olarak da gerçekleşen [JSON](https://en.wikipedia.org/wiki/JSON)' dır. Bu, NoSQL belge mağazalarını karmaşık mikro hizmet uygulamalarında hem kalıcılık hem de eşitleme (olay kaynağını belirleme [düzenlerini](https://en.wikipedia.org/wiki/Event-driven_architecture)kullanarak) için çok daha sorunsuz bir şekilde sığdırmasını sağlar. Daha geleneksel ilişkisel veritabanları, bu mimarilerde sürdürmek için çok daha karmaşık olabilir. Bunun nedeni, API 'lerde hem durum hem de eşitleme için gereken daha büyük dönüştürme miktarıdır. Özellikle Azure Cosmos DB, çok sayıda NoSQL veritabanından JSON tabanlı mikro hizmet mimarileri için daha sorunsuz bir şekilde uyum sağlayan çeşitli özelliklere sahiptir:

* saf JSON veri türleri seçimi
* veritabanına yerleşik bir JavaScript altyapısı ve [sorgu API 'si](./javascript-query-api.md) .
* bir kapsayıcıda yapılan değişiklikler hakkında bildirim almak için istemcilerin abone olabileceği bir grafik durumu [değişikliği akışı](./change-feed.md) .

## <a name="some-challenges-with-nosql-databases"></a>NoSQL veritabanlarıyla ilgili bazı sorunlar

NoSQL veritabanlarını uygularken bazı avantajlar olsa da, dikkate almanız isteyebileceğiniz bazı sorunlar da vardır. Bu, ilişkisel modelle çalışırken aynı ölçüde bulunmayabilir:

* aynı varlığa işaret eden çok sayıda ilişki içeren işlemler.
* Tüm veri kümesi genelinde güçlü tutarlılık gerektiren işlemler.

İlk sınamayı inceledikten sonra, NoSQL veritabanlarındaki parmak izi, daha önce bahsedilen ve dağıtılmış bir sistemde daha verimli okumalar üreten bir şekilde genel kullanıma sunulmuştur. Ancak, bu yaklaşımla birlikte oynatacak bazı tasarım zorlukları vardır. Tek bir kategori ve birden çok etiketle ilgili bir ürüne örnek alalım:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Birleştirmeler":::

NoSQL belge veritabanında en iyi yöntem yaklaşımı, kategori adı ve etiket adlarını doğrudan bir "ürün belgesi" içinde kabul etmek olacaktır. Ancak, kategorileri, etiketleri ve ürünleri eşitlenmiş halde tutmak için, verilerin bir "bire çok" ilişkisinde basit bir güncelleştirme olması yerine ürün içindeki birden çok kayıtta yinelendiği ve verileri almak için bir JOIN olduğundan, bu işlemi kolaylaştırmak için tasarım seçenekleri, bakım karmaşıklığı ekledi. 

Bir denge, yoğun olarak kullanılan kayıt sırasında okumaların daha verimlidir ve kavramsal olarak katılmış varlıkların sayısı arttıkça giderek daha verimli hale gelir. Ancak, artan bir kayıttaki birleştirilmiş varlıkların sayısını artırarak okuma verimliliği arttıkça, varlıkların eşitlenmiş durumda tutulması için de bakım karmaşıklığı da artar. Bu ticareti azaltıcı bir yol, [karma veri modeli](./modeling-data.md#hybrid-data-models)oluşturmaktır.

NoSQL veritabanlarında bu ticaretle başa çıkmak için daha fazla esneklik olsa da, artan esneklik daha fazla tasarım kararı verebilir. [Bir gerçek dünya örneği kullanarak Azure Cosmos DB verileri modelleyerek ve bölümleyerek](./how-to-model-partition-example.md), kullanıcıların yalnızca farklı bölümlerde, ancak farklı kapsayıcılarda yer aldıkları yerde, [eşitlenmiş Kullanıcı verilerinin eşzamanlı](./how-to-model-partition-example.md#denormalizing-usernames) tutulması için bir yaklaşım içeren, bu makaleye bakın.

Güçlü tutarlılığa göre, bunun tüm veri kümesi için gerekli olacağı nadir bir durumdur. Ancak, bunun gerekli olduğu durumlarda, dağıtılmış veritabanlarında bir zorluk olabilir. Güçlü tutarlılık sağlamak için, istemcilerin bu verileri okumasına izin vermeden önce tüm çoğaltmalarda ve bölgelerde eşitlenmesi gerekir. Bu, okuma gecikmesini artırabilir.

Azure Cosmos DB, burada alakalı olan çeşitli olanaklar için ilişkisel veritabanlarından daha fazla esneklik sunar, ancak küçük ölçekli uygulamalar için bu yaklaşım daha fazla tasarım konusunda dikkat edebilir. Bu konuda daha fazla ayrıntı için [tutarlılık, kullanılabilirlik ve performans avantajları](./consistency-levels.md) hakkında makalemize bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos hesabınızı ve diğer kavramları yönetmeyi öğrenin:

* [Azure Cosmos hesabınızı yönetme](how-to-manage-database-account.md)
* [Genel dağıtım](distribute-data-globally.md)
* [Tutarlılık düzeyleri](consistency-levels.md)
* [Azure Cosmos kapsayıcıları ve öğeleriyle çalışma](account-databases-containers-items.md)
* [Azure Cosmos hesabınız için VNET hizmeti uç noktası](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos hesabınız için IP-güvenlik duvarı](how-to-configure-firewall.md)
* [Azure Cosmos hesabınıza Azure bölgelerini ekleme ve kaldırma](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA 'Lar](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)