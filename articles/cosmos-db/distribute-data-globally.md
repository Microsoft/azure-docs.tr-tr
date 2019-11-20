---
title: Azure Cosmos DB ile verileri küresel olarak dağıtma
description: Küresel olarak dağıtılmış, çok modelli bir veritabanı hizmeti olan Azure Cosmos DB genel veritabanlarını kullanarak Planet-ölçek coğrafi çoğaltma, çok yöneticili, yük devretme ve veri kurtarma hakkında bilgi edinin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 4f17fc7df5aef449c3b0f6dd8d02ae58df959070
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384897"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Azure Cosmos DB genel bakış ile küresel veri dağıtımı

Bugünün uygulamalarının yüksek oranda yanıt vermesi ve her zaman çevrimiçi olması gerekir. Düşük gecikme süresi ve yüksek kullanılabilirlik elde etmek için, bu uygulamaların örneklerinin kullanıcılarına yakın olan veri merkezlerinde dağıtılması gerekir. Bu uygulamalar genellikle birden çok veri merkezinde dağıtılır ve global olarak dağıtılır. Küresel olarak dağıtılmış uygulamalar, uygulamaların kullanıcılarına yakın verilerin bir kopyası üzerinde çalışmasını sağlamak için dünyanın her yerindeki verileri saydam bir şekilde çoğaltabilen, küresel olarak dağıtılmış bir veritabanı gerektirir. 

Azure Cosmos DB, düşük gecikme süresi, esnek verimlilik, veri tutarlılığı için iyi tanımlanmış semantik ve yüksek kullanılabilirlik sağlamak üzere tasarlanan küresel olarak dağıtılmış bir veritabanı hizmetidir. Kısacası, uygulamanızın dünyanın her yerinden garantili hızlı yanıt süresine ihtiyacı varsa, her zaman çevrimiçi olması gerekiyorsa ve verimlilik ve depolama için sınırsız ve esnek ölçeklenebilirlik gerektiriyorsa, uygulamanızı Azure Cosmos DB oluşturmalısınız.

Veritabanlarınızı küresel olarak dağıtmak ve Azure bölgelerinde kullanılabilir olacak şekilde yapılandırabilirsiniz. Gecikme süresini azaltmak için, verileri kullanıcılarınızın bulunduğu yere yakın konuma koyun. Gerekli bölgelerin seçilmesi, uygulamanızın küresel erişime ve kullanıcılarınızın bulunduğu yere bağlıdır. Cosmos DB, verileri, Cosmos hesabınızla ilişkili tüm bölgelere şeffaf olarak çoğaltır. Bu, genel olarak dağıtılmış Azure Cosmos veritabanınızın ve, uygulamanızın yerel olarak okuyabilmesini ve yazabilme kapsayıcılarınızın tek bir sistem görüntüsünü sağlar. 

Azure Cosmos DB, hesabınızla ilişkili bölgeleri dilediğiniz zaman ekleyebilir veya kaldırabilirsiniz. Bir bölgeyi eklemek veya kaldırmak için uygulamanızın duraklatılması ya da yeniden dağıtılması gerekmez. Hizmetin yerel olarak sağladığı çoklu barındırma özellikleri nedeniyle her zaman yüksek oranda kullanılabilir olmaya devam eder.

![Yüksek oranda kullanılabilir dağıtım topolojisi](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Küresel dağıtımın temel avantajları

**Genel etkin-etkin uygulamalar oluşturun.** No, çok yöneticili çoğaltma protokolüyle her bölge yazma ve okuma işlemlerini destekler. Çoklu yönetici özelliği de şunları sunar:

- Sınırsız elastik yazma ve okuma ölçeklenebilirliği. 
- dünyanın her yerindeki% 99,999 okuma ve yazma kullanılabilirliği.
- % 99 ' luk yüzdede 10 milisaniyeden kısa bir süre içinde sunulan garantili okuma ve yazma işlemleri.

Azure Cosmos DB çoklu barındırmalı API 'Leri kullanarak, uygulamanız en yakın bölgeyi algılar ve istekleri bu bölgeye gönderebilir. En yakın bölge herhangi bir yapılandırma değişikliği olmadan tanımlanır. Azure Cosmos hesabınıza/konumundan bölge ekleyip kaldırırken uygulamanızın yeniden dağıtılması veya duraklatılması gerekmez, her zaman yüksek oranda kullanılabilir olmaya devam eder.

**Yüksek oranda yanıt veren uygulamalar oluşturun.** Uygulamanız, veritabanınız için seçtiğiniz tüm bölgelere karşı neredeyse gerçek zamanlı okuma ve yazma işlemleri gerçekleştirebilir. Azure Cosmos DB, seçtiğiniz düzeyin tutarlılık düzeyi garantisi olan bölgeler arasındaki veri çoğaltmasını dahili olarak işler.

**Yüksek oranda kullanılabilir uygulamalar oluşturun.** Bir veritabanını birden çok bölgede çalıştırmak, bir veritabanının kullanılabilirliğini artırır. Bir bölge kullanılamaz durumdaysa, diğer bölgeler uygulama isteklerini otomatik olarak işler. Azure Cosmos DB, çok bölgeli veritabanları için% 99,999 okuma ve yazma kullanılabilirliği sunmaktadır.

**Bölgesel kesintiler sırasında iş devamlılığını koruyun.** Azure Cosmos DB, bölgesel bir kesinti sırasında [otomatik yük devretmeyi](how-to-manage-database-account.md#automatic-failover) destekler. Bölgesel bir kesinti sırasında, Azure Cosmos DB gecikme süresi, kullanılabilirliği, tutarlılığı ve üretilen iş SLA 'larını korumaya devam eder. Tüm uygulamanızın yüksek oranda kullanılabilir olduğundan emin olmak için Cosmos DB, bölgesel bir kesinti benzetimi yapmak üzere el ile yük devretme API 'SI sunar. Bu API 'yi kullanarak normal iş sürekliliği ayrıntılarına sahip olabilirsiniz.

**Okuma ve yazma performansını küresel olarak ölçeklendirin.** Her bölgenin yazılabilir olmasını ve esnek ölçek okuma ve yazma işlemlerini dünyanın dört bir yanındaki yazmalarını sağlayabilirsiniz. Uygulamanızın bir Azure Cosmos veritabanında veya bir kapsayıcısının yapılandırdığı aktarım hızı, Azure Cosmos hesabınızla ilişkili tüm bölgelerde teslim edilmesi garanti edilir. Sağlanan aktarım hızı, [Mali olarak desteklenen SLA 'lara](https://aka.ms/acdbsla)göre garanti edilir.

**İyi tanımlanmış birkaç tutarlılık modeli arasından seçim yapın.** Azure Cosmos DB çoğaltma protokolü, beş iyi tanımlanmış, pratik ve sezgisel tutarlılık modelleri sunar. Her modelde tutarlılık ve performans arasında bir zorunluluğunu getirir bulunur. Bu tutarlılık modellerini kolayca, küresel olarak dağıtılmış uygulamalar oluşturmak için kullanın.

## <a id="Next Steps"></a>Sonraki adımlar

Aşağıdaki makalelerde genel dağıtım hakkında daha fazla bilgi edinin:

* [Küresel dağıtım-alt düzey](global-dist-under-the-hood.md)
* [Uygulamalarınızda çoklu yönetici yapılandırma](how-to-multi-master.md)
* [İstemcileri çoklu barındırma için yapılandırma](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Azure Cosmos DB hesabınızdan bölge ekleme veya kaldırma](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [SQL API hesapları için özel bir çakışma çözümleme ilkesi oluşturma](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Cosmos DB 'da programlanabilir tutarlılık modelleri](consistency-levels.md)
* [Uygulamanız için doğru tutarlılık düzeyi seçme](consistency-levels-choosing.md)
* [Azure Cosmos DB API'leri arasında tutarlılık düzeyleri](consistency-levels-across-apis.md)
* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans seçenekleri](consistency-levels-tradeoffs.md)
* [Daha yüksek kullanılabilirlik ve performans için iyileştirmek üzere özel eşitleme uygulama](how-to-custom-synchronization.md)
