---
title: Azure Cosmos DB ile verileri küresel olarak dağıtma
description: Küresel olarak dağıtılmış, çok modelli bir veritabanı hizmeti olan Azure Cosmos DB genel veritabanlarını kullanarak Planet-ölçek coğrafi çoğaltma, çok bölgeli yazma, yük devretme ve veri kurtarma hakkında bilgi edinin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/06/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: 6bdb167990afeb5c1b6c68185f24a8f930287bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102487908"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Azure Cosmos DB ile verilerinizi küresel ölçekte dağıtma
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Günümüzde uygulamaların çok kısa sürede yanıt vermesi ve her zaman açık olması beklenmektedir. Düşük gecikme süresi ve yüksek kullanılabilirlik düzeylerine erişmek için bu uygulamaların örneklerinin kullanıcılara yakın veri merkezlerine dağıtılması gerekir. Bu uygulamalar genelde birden fazla veri merkezinde dağıtılır ve bunlara genel olarak dağıtılmış adı verilir. Genel olarak dağıtılmış uygulamalar, uygulamaların kullanıcılarına yakın olan veri kopyalarında çalışmasına olanak tanımak için verileri dünyanın her yerinde saydam olarak çoğaltabilen veritabanlarına ihtiyaç duyar. 

Azure Cosmos DB küresel olarak dağıtılan ve veritabanınızın yerel çoğaltmalarından veri okumanıza ve yazmanıza olanak tanıyan bir veritabanı sistemidir. Azure Cosmos DB, verilerinizi Cosmos hesabınızla ilişkilendirilmiş tüm bölgelere saydam olarak çoğaltır. Azure Cosmos DB, düşük gecikme süresi, esnek verimlilik, veri tutarlılığı için iyi tanımlanmış semantik ve yüksek kullanılabilirlik sağlamak üzere tasarlanan küresel olarak dağıtılmış bir veritabanı hizmetidir. Kısacası, uygulamanızın dünyanın her yerinden hızlı yanıt süresine ihtiyacı varsa, her zaman çevrimiçi olması gerekiyorsa ve verimlilik ve depolama için sınırsız ve esnek ölçeklenebilirlik gerektiriyorsa, uygulamanızı Azure Cosmos DB oluşturmalısınız.

Veritabanlarınızı küresel olarak dağıtmak ve Azure bölgelerinde kullanılabilir olacak şekilde yapılandırabilirsiniz. Gecikme süresini azaltmak için, verileri kullanıcılarınızın bulunduğu yere yakın konuma koyun. Gerekli bölgelerin seçilmesi, uygulamanızın küresel erişime ve kullanıcılarınızın bulunduğu yere bağlıdır. Cosmos DB, verileri, Cosmos hesabınızla ilişkili tüm bölgelere şeffaf olarak çoğaltır. Bu, genel olarak dağıtılmış Azure Cosmos veritabanınızın ve, uygulamanızın yerel olarak okuyabilmesini ve yazabilme kapsayıcılarınızın tek bir sistem görüntüsünü sağlar.

Azure Cosmos DB, hesabınızla ilişkili bölgeleri dilediğiniz zaman ekleyebilir veya kaldırabilirsiniz. Bir bölge eklemek veya kaldırmak için uygulamanızın duraklatılması veya yeniden dağıtılması gerekmez.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Yüksek oranda kullanılabilir dağıtım topolojisi" border="false":::

## <a name="key-benefits-of-global-distribution"></a>Küresel dağıtımın temel avantajları

**Genel etkin-etkin uygulamalar oluşturun.** No, çok bölgeli yazma çoğaltma protokolü ile her bölge yazma ve okuma işlemlerini destekler. Çok bölgeli yazma özelliği de şunları sunar:

- Sınırsız elastik yazma ve okuma ölçeklenebilirliği.
- dünyanın her yerindeki% 99,999 okuma ve yazma kullanılabilirliği.
- %99 ' luk yüzdede 10 milisaniyeden kısa bir süre içinde sunulan garantili okuma ve yazma işlemleri.

Azure Cosmos hesabınıza/konumundan bölge ekleyip kaldırırken uygulamanızın yeniden dağıtılması veya duraklatılması gerekmez, her zaman yüksek oranda kullanılabilir olmaya devam eder.

**Yüksek oranda yanıt veren uygulamalar oluşturun.** Uygulamanız, veritabanınız için seçtiğiniz tüm bölgelere karşı neredeyse gerçek zamanlı okuma ve yazma işlemleri gerçekleştirebilir. Azure Cosmos DB, seçtiğiniz düzeyin tutarlılık düzeyi garantisi olan bölgeler arasındaki veri çoğaltmasını dahili olarak işler.

**Yüksek oranda kullanılabilir uygulamalar oluşturun.** Bir veritabanını birden çok bölgede çalıştırmak, bir veritabanının kullanılabilirliğini artırır. Bir bölge kullanılamaz durumdaysa, diğer bölgeler uygulama isteklerini otomatik olarak işler. Azure Cosmos DB, çok bölgeli veritabanları için% 99,999 okuma ve yazma kullanılabilirliği sunmaktadır.

**Bölgesel kesintiler sırasında iş devamlılığını koruyun.** Azure Cosmos DB, bölgesel bir kesinti sırasında [otomatik yük devretmeyi](how-to-manage-database-account.md#automatic-failover) destekler. Bölgesel bir kesinti sırasında, Azure Cosmos DB gecikme süresi, kullanılabilirliği, tutarlılığı ve üretilen iş SLA 'larını korumaya devam eder. Tüm uygulamanızın yüksek oranda kullanılabilir olduğundan emin olmak için Cosmos DB, bölgesel bir kesinti benzetimi yapmak üzere el ile yük devretme API 'SI sunar. Bu API 'yi kullanarak normal iş sürekliliği ayrıntılarına sahip olabilirsiniz.

**Okuma ve yazma performansını küresel olarak ölçeklendirin.** Her bölgenin yazılabilir olmasını ve esnek ölçek okuma ve yazma işlemlerini dünyanın dört bir yanındaki yazmalarını sağlayabilirsiniz. Uygulamanızın bir Azure Cosmos veritabanı üzerinde yapılandırdığı üretilen iş hacmi veya bir kapsayıcı Azure Cosmos hesabınızla ilişkili tüm bölgelerde sağlanıyor. Sağlanan aktarım hızı, [Mali olarak desteklenen SLA 'lara](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)göre garanti edilir.

**İyi tanımlanmış birkaç tutarlılık modeli arasından seçim yapın.** Azure Cosmos DB çoğaltma protokolü, beş iyi tanımlanmış, pratik ve sezgisel tutarlılık modelleri sunar. Her modelde tutarlılık ve performans arasında bir zorunluluğunu getirir bulunur. Bu tutarlılık modellerini kolayca, küresel olarak dağıtılmış uygulamalar oluşturmak için kullanın.

## <a name="next-steps"></a><a id="Next Steps"></a>Sonraki adımlar

Aşağıdaki makalelerde genel dağıtım hakkında daha fazla bilgi edinin:

* [Genel dağıtım - başlık altında](global-dist-under-the-hood.md)
* [Uygulamalarınızda çok bölgeli yazmaları yapılandırma](how-to-multi-master.md)
* [İstemcileri çoklu barındırma için yapılandırma](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Azure Cosmos DB hesabınızdan bölge ekleme veya kaldırma](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [SQL API hesapları için özel bir çakışma çözümleme ilkesi oluşturma](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Cosmos DB 'da programlanabilir tutarlılık modelleri](consistency-levels.md)
* [Uygulamanız için doğru tutarlılık düzeyini seçin](./consistency-levels.md)
* [Azure Cosmos DB API 'lerde tutarlılık düzeyleri](./consistency-levels.md)
* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans avantajları](./consistency-levels.md)