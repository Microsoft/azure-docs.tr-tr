---
title: Azure Cosmos DB’ye giriş
description: Azure Cosmos DB hakkında bilgi edinin. Bu genel olarak dağıtılan çok modelli veritabanı; düşük gecikme süresi, esnek ölçeklenebilirlik, yüksek kullanılabilirlik için oluşturulmuştur ve NoSQL verileri için yerel destek sunar.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: f322a240835d86697ae18c984700f22ca7b00145
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882172"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB’ye hoş geldiniz

Bugünün uygulamalarının yüksek oranda yanıt vermesi ve her zaman çevrimiçi olması gerekir. Düşük gecikme süresi ve yüksek kullanılabilirlik elde etmek için, bu uygulamaların örneklerinin kullanıcılarına yakın olan veri merkezlerinde dağıtılması gerekir. Uygulamalar, yoğun saatlerde kullanımda olan büyük değişikliklere gerçek zamanlı olarak yanıt vermelidir, verileri daha fazla artırarak depolar ve bu verileri milisaniye cinsinden kullanıcılara açık hale getirir.

Azure Cosmos DB, Microsoft 'un genel olarak dağıtılmış, çok modelli veritabanı hizmetidir. Bir düğmeye tıklayarak Cosmos DB, dünyanın dört bir yanındaki çok sayıda Azure bölgesinde işleme ve depolamayı esnek ve bağımsız olarak ölçeklendirmenizi sağlar. SQL, MongoDB, Cassandra, tablolar veya Gremlin dahil en sevdiğiniz API 'nizi kullanarak, işleme ve depolamayı esnek ölçeklendirebilir ve hızlı, tek basamaklı milisaniyelik veri erişiminden yararlanabilirsiniz. Cosmos DB, üretilen iş, gecikme süresi, kullanılabilirlik ve tutarlılık garantisi için, başka bir veritabanı hizmeti teklifi olmayan kapsamlı [hizmet düzeyi sözleşmeleri](https://aka.ms/acdbsla) (SLA) sağlar.

[Azure Cosmos DB’yi ücretsiz olarak](https://azure.microsoft.com/try/cosmosdb/) bir Azure aboneliği olmadan, ücretsiz ve herhangi bir taahhütte bulunmadan deneyebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos DB’yi ücretsiz olarak deneyin](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB, Microsoft'un esnek ölçek genişletme, garantili düşük gecikme süresi, beş tutarlılık modeli ve kapsamlı SLA garantisi ile genel olarak dağıtılmış veritabanı hizmetidir](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Önemli Avantajlar

### <a name="turnkey-global-distribution"></a>Anahtar teslim küresel dağıtım

Cosmos DB, dünya çapındaki yüksek oranda yanıt veren ve yüksek oranda kullanılabilir uygulamalar oluşturmanıza olanak sağlar. Cosmos DB kullanıcılarınız nerede olursa olsun verilerinizi saydam bir şekilde çoğaltır, kullanıcılarınız kendilerine en yakın verilerin bir çoğaltmayla etkileşime geçebilir.

Cosmos DB, her zaman bir düğmeye tıklayarak Azure bölgelerini Cosmos hesabınıza eklemenize veya kaldırmanıza olanak sağlar. Cosmos DB, uygulamanız yüksek oranda kullanılabilir olmaya devam ederken, hizmetin *çok sayıda barındırma* özelliği sayesinde verilerinizi Cosmos hesabınızla ilişkili tüm bölgelere sorunsuz bir şekilde çoğaltacaktır. Daha fazla bilgi için bkz. [genel dağıtım](distribute-data-globally.md) makalesi.

### <a name="always-on"></a>Her Zaman Açık

Azure altyapısı ve [saydam çok yöneticili çoğaltma](global-dist-under-the-hood.md)ile derin tümleştirme sayesinde, Cosmos DB hem okuma hem de yazma işlemleri için [% 99,999 yüksek kullanılabilirlik](high-availability.md) sağlar. Cosmos DB Ayrıca, Cosmos hesabınızın bölgesel yük devretmesini çağırmak için programlı bir şekilde (veya Portal aracılığıyla) olanak sağlar. Bu özellik, uygulamanızın, bölgesel olağanüstü durum durumunda yük devretmeye yönelik olarak tasarlandığından emin olmanıza yardımcı olur.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Verimlilik ve depolama için esnek ölçeklenebilirlik, dünya çapındaki

Saydam yatay bölümlendirme ve çok yöneticili çoğaltma ile tasarlanan Cosmos DB, dünyanın her yerindeki yazma ve okumalarınız için dışı elastik ölçeklenebilirlik sunar. Tek bir API çağrısıyla, tek bir API çağrısıyla binlerce ve yüzlerce milyonlarca istek/sn üzerinden ölçeği esnek dönüştürebilirsiniz ve yalnızca ihtiyacınız olan aktarım hızı (ve depolama alanı) için ödeme yaparsınız. Bu özellik, en yüksek düzeyde sağlama yapmanıza gerek kalmadan iş yüklerinizde beklenmedik ani artışlar ile uğraşmanıza yardımcı olur. Daha fazla bilgi için bkz. [Cosmos DB bölümünde bölümlendirme](partitioning-overview.md), [kapsayıcılar ve veritabanlarında sağlanan aktarım hızı](set-throughput.md)ve [sağlanan aktarım hızını küresel olarak ölçeklendirme](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>99. yüzdede garantili düşük gecikme süresi, dünya çapındaki

Cosmos DB kullanarak yüksek oranda duyarlı, dünya ölçekli uygulamalar oluşturabilirsiniz. No, çok yöneticili çoğaltma Protokolü ve mandal içermeyen ve [yazma için iyileştirilmiş veritabanı altyapısından](index-policy.md), Cosmos DB her ikisi için 10 MS 'den daha az gecikme süresi sağlar. Bu özellik, verilerin sürekli olarak içeri aktarılmasını ve yüksek oranda yanıt veren uygulamalar için hızlı sorgular yapmanızı mümkün hale getirmenizi sunar.

### <a name="precisely-defined-multiple-consistency-choices"></a>Tam olarak tanımlanmış, birden çok tutarlılık seçeneği

Cosmos DB ' de küresel olarak dağıtılmış uygulamalar oluştururken, artık [tutarlılık, kullanılabilirlik, gecikme süresi ve aktarım hızı arasında](consistency-levels-tradeoffs.md)aşırı bir denge yapmanız gerekmez. Cosmos DB, çok yöneticili çoğaltma protokolü, *güçlü*, *sınırlanmış Eskime durumu*, *oturum*, *tutarlı ön ek*ve *nihai* gibi [beş iyi tanımlanmış tutarlılık - seçeneği](consistency-levels.md) sunmak üzere dikkatle tasarlanmıştır. küresel olarak dağıtılan uygulamanız için düşük gecikme süresi ve yüksek kullanılabilirliğe sahip sezgisel bir programlama modeli için.

### <a name="no-schema-or-index-management"></a>Şema veya dizin yönetimi yok

Veritabanı şemasının ve dizinlerinin bir uygulamanın şemasıyla eşitlenmesi, genel olarak dağıtılmış uygulamalar için özellikle ölçeklenebilir hale getirin. Cosmos DB, şema veya dizin yönetimiyle uğraşmanız gerekmez. Veritabanı altyapısı tam olarak şemadan bağımsızdır.  Şema ve dizin yönetimi gerekli olmadığından, şemaları geçirirken uygulama kesinti süresi hakkında endişelenmenize gerek kalmaz. Cosmos DB [tüm verileri otomatik olarak dizinler](index-policy.md) ve sorguları hızlı bir şekilde sunar.

### <a name="battle-tested-database-service"></a>Test edilen veritabanı hizmeti

Cosmos DB, Azure 'da temel bir hizmettir. Neredeyse bir yıllık yılda, Microsoft 'un Skype, Xbox, Office 365, Azure ve diğer birçok konuda, küresel ölçekte görev açısından kritik uygulamalar için pek çok ürünü tarafından kullanılan Cosmos DB kullanılmıştır. Günümüzde, Azure 'daki en hızlı büyüyen hizmetlerden biri olan Cosmos DB çok sayıda harici müşteri tarafından kullanılan ve esnek ölçek, anahtar genel dağıtım, çok yöneticili çoğaltma, her ikisinin de düşük gecikme süresi ve yüksek kullanılabilirlik gerektiren iş açısından kritik uygulamalar tarafından kullanılır. okuma ve yazma işlemleri.

### <a name="ubiquitous-regional-presence"></a>Ubititous bölgesel varlık

Cosmos DB, genel bulutta 54 + bölgeleri, [Azure Çin 21Vianet](https://www.azure.cn/en-us/), Azure Almanya, Azure Kamu ve Savunma Bakanlığı (DOD) Için Azure Kamu dahil olmak üzere tüm dünyada Azure bölgelerinde kullanılabilir. [Cosmos DB bölgesel varlığına](regional-presence.md)bakın.

### <a name="secure-by-default-and-enterprise-ready"></a>Varsayılan ve kurumsal hazır ile güvenli

Cosmos DB, [çok sayıda uyumluluk standardı dizisi](compliance.md)için sertifikalandırilmiştir. Ayrıca, Cosmos DB tüm veriler REST ve hareket halinde şifrelenir. Cosmos DB, satır düzeyi yetkilendirme sağlar ve katı güvenlik standartlarına uyar.

### <a name="significant-tco-savings"></a>Önemli TCO tasarrufu

Cosmos DB tam olarak yönetilen bir hizmet olduğundan, artık veritabanı yazılımınızın karmaşık çoklu veri merkezi dağıtımlarını ve yükseltmelerini yönetmeniz ve çalıştırmanız, destek, lisanslama veya işlemler için ödeme yapmanız veya en yoğun iş yükü için veritabanınızı sağlamanız gerekir. Daha fazla bilgi için bkz. [Cosmos DB maliyeti iyileştirme](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Sektör lideri kapsamlı SLA 'Lar

Cosmos DB, [sektör lideri kapsamlı SLA 'lar](https://azure.microsoft.com/support/legal/sla/cosmos-db/) sunan ilk ve tek hizmettir:% 99,999 yüksek kullanılabilirlik, okuma ve yazma gecikme süresi 99. yüzdebirlik, garantili aktarım hızı ve tutarlılık.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Yerel olarak yerleşik Apache Spark, genel olarak dağıtılmış işlemsel analiz ve AI

[Spark](spark-connector.md) 'ı, Cosmos DB depolanan veriler üzerinde doğrudan çalıştırabilirsiniz. Bu özellik, doğrudan Cosmos DB karşı çalışan işlem iş yüklerini etkilemeden küresel ölçekte düşük gecikmeli, işlemsel çözümlemeler yapmanıza olanak sağlar. Daha fazla bilgi için bkz. [küresel olarak dağıtılmış işlemsel analiz](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Popüler açık kaynak yazılım (OSS) API 'Lerini kullanarak Cosmos DB uygulama geliştirme

Cosmos DB, Cosmos veritabanınızda depolanan verilerinize göre çalışan bir API seçimi sunmaktadır. Varsayılan olarak, Cosmos veritabanınızı sorgulamak için SQL (çekirdek API) [kullanabilirsiniz](how-to-sql-query.md) . Cosmos DB, [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) ve [Azure Tablo depolama](table-introduction.md)için API 'leri de uygular. Yaygın olarak kullanılan NoSQL (örn., MongoDB, Cassandra, Gremlin) için istemci sürücülerini (ve araçları) doğrudan Cosmos veritabanınıza işaret edebilirsiniz. Yaygın olarak kullanılan NoSQL API 'Lerinin tel protokollerini destekleyerek, Cosmos DB şunları yapmanıza olanak sağlar:

* Uygulama mantığınızın önemli kısımlarını korurken uygulamanızı Cosmos DB 'e kolayca geçirin.
* Uygulamanızı taşınabilir tutun ve bulut satıcısı belirsiz olmaya devam edin.
* Yaygın NoSQL API 'Leri için endüstri lideri, mali olarak desteklenen SLA 'Lar ile tam olarak yönetilen bir bulut hizmeti alın. 
* Esnek, veritabanlarınıza göre sağlanan aktarım hızını ve depolamayı ölçeklendirebilir ve yalnızca ihtiyacınız olan aktarım hızı ve depolama için ödeme yaparsınız. Bu, önemli maliyet tasarruflarına yol açar.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Azure Cosmos DB'den yararlanan çözümler

Çeşitli veriler için neredeyse gerçek zamanlı yanıt süreleriyle [küresel ölçekte](distribute-data-globally.md) çok büyük miktarlarda veri, okuma ve yazma işlemlerini işlemesi gereken tüm [Web, mobil, oyun ve ıot uygulamaları](use-cases.md) , Cosmos DB [garanti edilen yüksek kullanılabilirliğinden faydalanır ](https://azure.microsoft.com/support/legal/sla/cosmos-db/), yüksek aktarım hızı, düşük gecikme süresi ve ayarlanabilir tutarlılık. Azure Cosmos DB [IoT ve telematik](use-cases.md#iot-and-telematics), [Perakende ve pazarlama](use-cases.md#retail-and-marketing), [oyun](use-cases.md#gaming) ve [Web ve mobil uygulamalar](use-cases.md#web-and-mobile-applications)oluşturmak için nasıl kullanılabileceğinizi öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

Cosmos DB temel kavramları [anahtar oluşturma genel dağıtım](distribute-data-globally.md) ve [bölümlendirme](partitioning-overview.md) ve [sağlanan aktarım hızı](request-units.md)hakkında daha fazla bilgi edinin.

Dört hızlı başlangıçtan biriyle Azure Cosmos DB kullanmaya başlayın:

* [Azure Cosmos DB SQL API’yi kullanmaya başlama](create-sql-api-dotnet.md)
* [MongoDB için Azure Cosmos DB API 'SI ile çalışmaya başlama](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API’yi kullanmaya başlama](create-cassandra-dotnet.md)
* [Azure Cosmos DB Graph API’yi kullanmaya başlama](create-graph-dotnet.md)
* [Azure Cosmos DB Tablo API’yi kullanmaya başlama](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB’yi ücretsiz deneyin](https://azure.microsoft.com/try/cosmosdb/)
