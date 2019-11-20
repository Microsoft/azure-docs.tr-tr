---
title: Azure Cosmos DB fiyatlandırma modeli
description: Bu makalede, Azure Cosmos DB fiyatlandırma modeli ve maliyet yönetimi ile maliyet planlamasını nasıl basitleştireceğinizi açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0e0adef2728ef75dc67f02fdf38b6638965df62f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756863"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB fiyatlandırma modeli 

Azure Cosmos DB fiyatlandırma modeli, maliyet yönetimi ve planlamasını basitleştirir. Azure Cosmos DB, sağlanan aktarım hızı ve kullandığınız depolama alanı için ödeme yaparsınız.

* **Sağlanan aktarım hızı**: sağlanan aktarım hızı (Ayrıca, ayrılmış aktarım hızı olarak da anılır), herhangi bir ölçekte yüksek İhtiyaç duyduğunuz aktarım hızını (RU/s) ve yapılandırılan aktarım hızını güvence altına almak için gereken kaynakları ayıran Azure Cosmos DB belirlersiniz. Verilen bir saat için sağlanan maksimum üretilen iş hacmi için saatlik olarak faturalandırılırsınız.

   > [!NOTE]
   > Sağlanan aktarım hızı modeli, kaynakları kapsayıcınıza veya veritabanınıza ayırır, ancak herhangi bir iş yükü çalıştırmasa bile sağlanan aktarım hızı için ücretlendirilirsiniz.

* **Tüketilen depolama alanı**: belirli bir saat için veriler ve dizinler için tüketilen toplam depolama miktarı (GB) için sabit bir ücret faturalandırılırsınız.

Saniye başına [Istek birimi](request-units.md) (ru/s) olarak belirtilen sağlanan aktarım hızı, kapsayıcılardan veya veritabanlarına veri yazmanızı veya bunları yazmanıza izin verir. [Bir veritabanı ya da kapsayıcı üzerinde üretilen iş](set-throughput.md)sağlayabilirsiniz. İş yükünüzün gereksinimlerine bağlı olarak, aktarım hızını dilediğiniz zaman yukarı/aşağı ölçeklendirebilirsiniz. Azure Cosmos DB fiyatlandırması elastik ve bir veritabanı ya da kapsayıcıda yapılandırdığınız aktarım hızına göre orantılıdır. En düşük aktarım hızı ve depolama değerleri ve ölçek artımları, küçük ölçekte büyük ölçekli kapsayıcılara kadar tüm müşterilerin segmentlerine yönelik eksiksiz bir fiyat ve elaze yelpazesi sağlar. Her veritabanı veya kapsayıcı, en az 400 RU/sn ve GB cinsinden tüketilen depolama alanı 100 RU/sn birimlerinde sağlanan aktarım hızı için saatlik olarak faturalandırılır. Sağlanan aktarım hızının aksine, depolama, Tüketim esasına göre faturalandırılır. Diğer bir deyişle, herhangi bir depolamayı önceden ayırmanız gerekmez. Yalnızca kullandığınız depolama alanı için faturalandırılırsınız.

Daha fazla bilgi için [Azure Cosmos DB fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın ve [Azure Cosmos DB faturanızı](understand-your-bill.md)anlayın.

Azure Cosmos DB fiyatlandırma modeli tüm API 'lerde tutarlıdır. Daha fazla bilgi edinmek için bkz. [Azure Cosmos DB fiyatlandırma modelinin müşteriler için uygun maliyetli olması](total-cost-ownership.md). SLA 'Ları sağlamak için bir veritabanında veya kapsayıcıda gereken en düşük aktarım hızı vardır ve her 100 RU/s için $6 ile sağlanan üretilen işi artırabilir veya azaltabilirsiniz.

Şu anda hem veritabanı hem de kapsayıcı tabanlı aktarım hızı için en düşük fiyat değeri $24/ay olur (en son bilgiler için [Azure Cosmos DB fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın. İş yükünüz birden çok kapsayıcı kullanıyorsa, veritabanı düzeyinde aktarım hızı veritabanı düzeyinde aktarım hızı kullanılarak maliyet için iyileştirilebilir, çünkü veritabanı düzeyi işleme kapsayıcılar arasındaki aktarım hızını paylaşan bir veritabanında herhangi bir kapsayıcı kullanmanıza izin verir. Aşağıdaki tablo, sağlanan aktarım hızını ve farklı varlıkların maliyetlerini özetler:

|**Varlığının**  | **Minimum verimlilik & maliyeti** |**Ölçek artışlarını & maliyeti** |**Sağlama kapsamı** |
|---------|---------|---------|-------|
|Database    | 400 RU/sn (24/ay)    | 100 RU/sn (6/ay)   |İşleme veritabanı için ayrılmıştır ve veritabanı içindeki kapsayıcılar tarafından paylaşılır |
|Kapsayıcı     | 400 RU/sn (24/ay)    | 100 RU/sn (6/ay)  |Aktarım hızı belirli bir kapsayıcı için ayrılmıştır |

Önceki tabloda gösterildiği gibi, Azure Cosmos DB ' deki en düşük aktarım hızı, $24/ay fiyatı üzerinden başlar. En düşük aktarım hızı ile başlayıp, üretim iş yüklerinizi desteklemek için zaman içinde ölçeği artırdıysanız, maliyetleriniz $6/ay artışlarla düzgün bir şekilde artar. Azure Cosmos DB fiyatlandırma modeli esnektir ve ölçeği büyütme veya küçültme sırasında fiyata yönelik yumuşak bir artış veya azalma vardır.

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB’yi ücretsiz deneyin 

Azure Cosmos DB, geliştiriciler için ücretsiz olarak çeşitli seçenekler sunar. Bu seçenekler şunlardır:

* **Ücretsiz Azure hesabı**: Azure, 12 ay boyunca ilk 30 gün boyunca Azure kredileri ve sınırlı sayıda ücretsiz hizmet sunan ücretsiz bir $200 [Katman](https://azure.microsoft.com/free/) sunmaktadır. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](../billing/billing-avoid-charges-free-account.md). Azure Cosmos DB Azure Ücretsiz hesabının bir parçasıdır. Özellikle Azure Cosmos DB için bu ücretsiz hesap, tüm yıl boyunca 5 GB depolama ve 400 ru iş hacmi sunmaktadır. 

* **Ücretsiz Azure Cosmos DB deneyin**: Azure Cosmos DB ücretsiz hesaplar için TRY Azure Cosmos DB kullanarak zaman sınırlı bir deneyim sunar. Hızlı başlangıçları ve öğreticileri kullanarak bir Azure Cosmos DB hesabı oluşturabilir, veritabanı ve koleksiyonlar oluşturabilir ve örnek bir uygulama çalıştırabilirsiniz. Örnek uygulamayı bir Azure hesabına abone olmadan veya kredi kartınızı kullanarak çalıştırabilirsiniz. Hesabınızı dilediğiniz zaman yenileyebilme olanağı sunan bir ayda [ücretsiz Azure Cosmos DB için Azure Cosmos DB deneyin](https://azure.microsoft.com/try/cosmosdb/) .

* **Azure Cosmos DB öykünücü**: Azure Cosmos DB öykünücü, geliştirme amacıyla Azure Cosmos DB hizmetine taklit eden yerel bir ortam sağlar. Öykünücü, ücretsiz olarak ve bulut hizmeti için yüksek uygunluğa sahip olarak sunulur. Azure Cosmos DB öykünücüsü kullanarak, Azure aboneliği oluşturmadan veya herhangi bir ücret ödemeden uygulamalarınızı yerel olarak geliştirebilir ve test edebilirsiniz. Üretim ortamına geçmeden önce öykünücüyü yerel olarak kullanarak uygulamalarınızı geliştirebilirsiniz. Öykünücüdeki uygulamanın işlevselliğine karşı memnun olduktan sonra, Bulutta Azure Cosmos DB hesabı ile geçiş yapabilir ve maliyeti önemli ölçüde tasarruf edebilirsiniz. Öykünücü hakkında daha fazla bilgi için bkz. [geliştirme ve test için Azure Cosmos DB kullanma](local-emulator.md) , daha fazla ayrıntı için.

## <a name="pricing-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip fiyatlandırma

Azure Cosmos DB [ayrılmış kapasite](cosmos-db-reserved-capacity.md) , bir yıl veya üç yılda Azure Cosmos DB kaynakları için ön ödeme yaparak paradan tasarruf etmenize yardımcı olur. Maliyetlerinizi bir yıllık veya üç yıllık ön taahhütte önemli ölçüde azaltabilir ve normal fiyatlandırmayla karşılaştırıldığında% 20-65 indirimle tasarruf edebilirsiniz. Azure Cosmos DB ayrılmış kapasite, bir yıllık veya üç yıllık bir dönem için sağlanan aktarım hızı (RU/s) için ön ödeme yaparak maliyetleri düşürmenize yardımcı olur ve sağlanan aktarım hızı hakkında bir indirim elde edersiniz. 

Ayrılmış kapasite bir faturalandırma indirimi sağlar ve Azure Cosmos DB kaynaklarınızın çalışma zamanı durumunu etkilemez. Ayrılmış kapasite, MongoDB, Cassandra, SQL, Gremlin ve Azure tabloları ile dünya çapındaki tüm bölgeleri içeren tüm API 'Ler için tutarlı bir şekilde kullanılabilir. Ayrılmış kapasite hakkında daha fazla bilgi edinmek [için, ayrılmış kapasite ile Azure Cosmos DB kaynaklar Için ön ödeme](cosmos-db-reserved-capacity.md) yapın ve [Azure Portal](https://portal.azure.com/)ayrılan kapasiteyi satın alabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde Azure Cosmos DB kaynaklarınızın maliyetlerini en iyi duruma getirme hakkında daha fazla bilgi edinebilirsiniz:

* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](optimize-cost-queries.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin
* [Ayrılmış Azure Cosmos DB kapasitesi](cosmos-db-reserved-capacity.md) hakkında bilgi edinin
* [Azure Cosmos DB öykünücüsü](local-emulator.md) hakkında bilgi edinin
