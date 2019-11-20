---
title: Azure Cosmos DB ile toplam sahip olma maliyeti (TCO)
description: Bu makalede, Azure Cosmos DB 'ın toplam sahip olma maliyeti IaaS ve şirket içi veritabanları ile karşılaştırılır
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754802"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Azure Cosmos DB ile toplam sahip olma maliyeti (TCO)

Azure Cosmos DB, ayrıntılı çok kiracılı ve kaynak İdaresi ile tasarlanmıştır. Bu tasarım, Azure Cosmos DB önemli ölçüde düşük maliyetli ve kullanıcıların kaydetmesine yardımcı olur. Şu anda Azure Cosmos DB, her zaman yoğunluğu ve bir küme içindeki binlerce müşteri iş yüklerini içeren tek bir makinede 280 ' den fazla müşteri iş yükünü destekler. BT yükü, müşteri iş yüklerinin çoğaltmalarını bir kümedeki farklı makinelerde ve bir veri merkezinde bulunan birden çok kümeye dengeler. Daha fazla bilgi edinmek için bkz. [Azure Cosmos DB: genel olarak dağıtılmış veritabanlarının en katmanını iletme](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Kaynak yönetimi, çok kiracılı ve Azure altyapısıyla ilgili yerel tümleştirme nedeniyle Azure Cosmos DB, MongoDB, Cassandra veya IaaS üzerinde çalışan diğer OSS NoSQL 'den 6 kat ve veritabanından en fazla 10 kez derleyici olmak üzere. Şirket içinde çalışan altyapılar. [Bir NoSQL veritabanı bulut hizmetinin toplam maliyetlerine (değil)](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)ait incelemeye bakın.

Apache Cassandra, MongoDB, HBase gibi OSS NoSQL veritabanı çözümleri, şirket içi için tasarlanmıştır. Yönetilen bir hizmet olarak sunulduklarında, sağlanan kümelerin ve izleme desteğinin yönetilmesi için kiracı veritabanı olan bir Kaynak Yöneticisi şablonuna eşdeğerdir. OSS NoSQL mimarileri önemli işlem yükü gerektirir ve uzmanlığın bulunması zor ve pahalı olabilir. Diğer taraftan Azure Cosmos DB, geliştiricilerin veritabanı altyapısını yönetmek ve sürdürmek yerine iş yeniliklerine odaklanmasını sağlayan, tam olarak yönetilen bir bulut hizmetidir. 

Bulut Yerel veritabanı hizmeti Azure Cosmos DB farklı olarak, OSS NoSQL veritabanı motorları, temel mimari ilkeleri olarak kaynak yönetimi veya ayrıntılı çok kiracılı bir şekilde tasarlanmadı ve derlenmedi. Cassandra ve MongoDB gibi OSS NoSQL veritabanı motorları, çalıştığı sanal makine kaynaklarının tümünün kullanımları için kullanılabilir olduğunu temel bir varsayımına sahiptir. Bu veritabanı altyapılarından birçoğu, kaynak miktarı belirli bir eşiğin altına düşerse çalışamaz. Örneğin, küçük sanal makine örnekleri için ve genellikle büyük ölçekli VM 'Ler için yüksek maliyetli sanal makineler öneren satıcı tarafından önerilen yapılandırmalarda kullanılabilir. Bu nedenle, bir OSS NoSQL veya başka bir şirket içi veritabanı motorunu barındırmak ve saniye başına istekler veya tüketilen depolama alanı gibi tüketim tabanlı bir ücretlendirme modeli kullanarak kullanılabilmesini sağlamak mümkün değildir.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Azure Cosmos DB toplam sahip olma maliyeti 

Azure Cosmos DB sunucusuz sağlama modeli, veritabanı altyapısının aşırı sağlanması gereksinimini ortadan kaldırır. Azure Cosmos DB kaynaklar, özelleştirilmiş yapılandırmalara veya lisanslamaya gerek kalmadan sağlanır. Sonuç olarak, Azure Cosmos DB ile desteklenen uygulamalar, OSS NoSQL veritabanlarına kıyasla yüzde 70 toplam sahiplik tasarrufuna kadar bir şekilde çalışabilir. Bazı gerçek zamanlı örnekler için bkz. [Müşteri kullanım örnekleri](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Azure Cosmos DB fiyatlandırma modelinin diğer avantajları şunlardır:

* **Fiyat Için harika değer:** Pazar analistleri, müşteriler ve iş ortakları, bu çözümleri kendi kendilerine veya diğer satıcılarla uygularken müşterilerin ne kadar düşük bir fiyat için sunduğu Azure Cosmos DB tüm özelliklerin daha fazla bir değerini doğruladı. Veritabanı, genel dağıtım, çok yöneticili, iyi tanımlanmış ve sezgisel tutarlılık modelleriyle ilgili özellikler, Otomatik Dizin oluşturma, hiçbir karmaşıklık, ek yük veya kesinti olmadan Azure Cosmos DB ile büyük ölçüde basitleştirilmiştir.

* **NoSQL DevOps yönetimi gerekmez:** Azure Cosmos DB, dağıtımları yönetmek, bakım, ölçek veya yama gerçekleştirmek için bir tane olan DevOps 'u kullanmak zorunda değildir. Şirket içinde veya bulut altyapısında barındırılan OSS NoSQL kümesi ile yaptığınız tüm iş yüklerini yürütebilirsiniz.

![Azure Cosmos DB mülkiyet maliyeti](./media/total-cost-ownership/tco.png)

* **Esnek ölçek özelliği:** Azure Cosmos DB üretilen iş miktarı yukarı ve aşağı ölçeklendirilebilir ve yoğun olmayan saatlerde sahiplik maliyetini düşürmenize olanak tanır. Bulut altyapısına dağıtılan OSS NoSQL kümeleri, sınırlı esneklik sunar ve şirket içi dağıtımlar tanıma göre elastik değildir. Azure Cosmos DB, daha fazla verimlilik sağlarsanız, üretilen iş hızının doğrusal olarak ölçeklendirilmesi garanti edilir. Bu garanti, finans SLA 'Ları ve her ölçekte 99. yüzdebirlik ile yedeklenir.

* **Ölçek ekonomileri:** Azure Cosmos DB gibi yönetilen bir hizmet, ağ, depolama ve hesaplar ile yerel olarak tümleşik olan çok sayıda düğüm ile çalışır. Azure Cosmos DB büyük ölçekli, standartlaştırma nedeniyle maliyetleri tasarrufu sağlayabilirsiniz.

* **Bulut Için Iyileştirildi:** Azure Cosmos DB, ayrıntılı çok kiracılı ve performans yalıtımıyla sıfırdan tasarlanmıştır. Bu, binlerce kiracının ve iş yüklerinin kümeler arasında ve veri merkezlerinde en iyi şekilde yerleştirilmesini, yürütülmesini ve dengelenmesini sağlar. Buna karşılık, OSS NoSQL veritabanlarının geçerli nesli, tek bir kiracının iş yükünü çalıştırmak için kabul edilen tüm sanal makine ile şirket içinde çalışır. Bu veritabanları aynı zamanda bir bulut sağlayıcısının altyapısını ve donanımını tam bir ölçüde artırmak üzere tasarlanmamıştır. Örneğin, bir OSS NoSQL veritabanı altyapısı, bir sanal makine, bir rutin görüntü yükseltmesine veya Premium diskin zaten üç yönlü olarak çoğaltıldığının farkında değildir. Bu avantajlardan faydalanabilir ve müşterilere avantajları ve tasarrufları geçmiyor.

* **Saate göre ödeme yaparsınız:** Zaman içinde herhangi bir noktada ölçeklendirilmesi gereken büyük ölçekli iş yükleri için yalnızca saate göre ücretlendirilirsiniz. Bir uygulamadaki iş yükleri genellikle yılın saatlerinde ve sorgulanan verilere göre farklılık gösterir. Azure Cosmos DB sayesinde ölçeği, ihtiyacınız olan şekilde değiştirebilir veya azaltabilirsiniz ve yalnızca ihtiyacınız olanlar için ödeme yapabilirsiniz. Şirket içi veya IaaS barındırılan sistemleri ile, her saat donanım yetkisini almanın bir yolu olmadığından bu modeli eşleştirebilirsiniz. Böyle durumlarda, büyük olasılıkla Azure Cosmos DB bir ortalama olarak 10 ila 14 kez tasarruf edebilirsiniz.

* **Ücretsiz olarak çok sayıda özellik alırsınız:** Azure Cosmos DB, yazma iş yükleri alternatif veritabanı hizmetleriyle karşılaştırıldığında önemli ölçüde daha fazla. Ayrıca, Azure Cosmos DB [Otomatik Dizin oluşturma](indexing-policies.md), [yaşam süresi (TTL)](time-to-live.md), [akışı değiştirme](change-feed.md) ve diğer veritabanı hizmetlerinin genellikle ücretlendirdiği ek ücretler olmadan diğer özellikleri sunar.

* **Farklı iş yükleri için Birleşik para birimini kullanır:** Alternatif tekliflerden farklı olarak, Azure Cosmos DB iş yüklerinin aksine, örneğin, okuma ve yazma işlemleri yapmak zorunda değilsiniz. Ya da okuma aktarım hızı ve yazma aktarım hızına sahip bir iş yükü türüne göre verimlilik sağlama. Azure Cosmos DB, sağlanan aktarım hızı, Istek birimleri veya RU/sn bakımından Birleşik ve normalleştirilmiş bir para birimi kullanılarak ayrılmıştır. Azure Cosmos DB iş yüklerinize öncelik atamanıza, kapasite planlama gerçekleştirmenize veya her bir kapasite türü için ödeme yapmaya zorlamaz tanımlanırlar. Bu yaklaşım, çeşitli işlemler ve iş yükü türleri arasında aynı RU/s 'yi kolayca değiş tokuş etmenize olanak sağlar.

* **Ölçek için sağlama VM 'lerinin gerekli değildir:** Çok sayıda işletimsel veritabanı, ölçeklendirme istiyorsanız, gürültülü komşuları ve gevşek kaynak idaresi için büyük sanal makinelerle gitmeniz gerekir. Bu, müşterilerin yükünü ve ön taahhüt taahhüt düzeyini getirir. Azure Cosmos DB ile, küçük ölçekli iş yükü boyutlarına sorunsuz bir şekilde başlayabilir ve herhangi bir kesinti veya veri kullanılabilirliği üzerinde bir etkisi olmaz.

* **Sağlanan aktarım hızını en yüksek sınıra kadar kullanabilirsiniz:** Azure Cosmos DB alt çekirdekli çoğullama 'nin Sanallaştırması tarafından, sağlanan aktarım hızını IaaS barındırılan seçenekleri veya üçüncü taraf teklifleriyle daha büyük bir ölçüde daha fazla olacak şekilde gri ayırabilirsiniz. Bu yöntem, alternatif çözümlerden çok daha fazlasını kaydeder.

* **Diğer Azure hizmetleriyle Azure Cosmos DB derin tümleştirme.** Azure Cosmos DB ağ, Işlem, Azure Işlevleri (sunucusuz), Azure IoT ve diğer Azure hizmetleriyle yerel bir tümleştirmeye sahiptir. Bu tümleştirmeyle, en iyi performansı elde etmek için güçlü garanti sayesinde dünya genelinde veri çoğaltmanın hızını elde edersiniz. Üçüncü taraf çözümleri, bu tür özellikleri sunmak üzere genellikle bir Premium ücret ödemeyecektir.

* **Varsayılan olarak en az 10-20 hata etki alanı ile yüksek kullanılabilirlik kazanın:** Azure Cosmos DB, yüksek kullanılabilirlik için kritik bir özellik olan hata etki alanları genelinde iş yüklerinin dağıtımını destekler. Bu, dünyanın her yerindeki 99. yüzdede okuma ve yazma işlemleri için 99,999 yüksek kullanılabilirlik sağlar. Kendi kendinize veya üçüncü taraf bir çözüm üzerinden buna benzer bir şekilde uygulama maliyeti yüksek olacaktır.

* **Tüm kurumsal özellikleri ek bir ücret ödemeden otomatik olarak alırsınız.** Azure Cosmos DB, bekleyen ve ek bir ücret ödemeden (yarışma kıyasla) en kapsamlı uyumluluk sertifikaları, güvenlik ve şifreleme kümesi sunar. Bölgesel kullanılabilirliği dünyanın herhangi bir yerinden otomatik olarak alırsınız. Veritabanınızı istediğiniz sayıda Azure bölgesine yayılabilir ve herhangi bir noktada bölge ekleyebilir veya kaldırabilirsiniz.

* **Ayrılmış kapasiteye sahip maliyetlerin %65 ' e kadar tasarruf edebilirsiniz:** Azure Cosmos DB [ayrılmış kapasite](cosmos-db-reserved-capacity.md) , bir yıl veya üç yılda Azure Cosmos DB kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. Maliyetlerinizi bir yıllık veya üç yıllık ön taahhütte önemli ölçüde azaltabilir ve normal fiyatlandırmayla karşılaştırıldığında% 20-65 indirimle tasarruf edebilirsiniz. Görev açısından kritik iş yüklerinizde, sağlama kapasitesi açısından daha iyi SLA 'Lar edinebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Fiyatlandırma modelinin müşteriler için uygun maliyetli Azure Cosmos DB](total-cost-ownership.md) hakkında daha fazla bilgi edinin
* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](optimize-cost-queries.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin
* [Bir NoSQL veritabanı bulut hizmeti 'Nin toplam maliyeti (değil)](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf) hakkında daha fazla bilgi edinin
