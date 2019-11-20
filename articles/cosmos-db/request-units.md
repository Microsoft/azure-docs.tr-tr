---
title: Azure Cosmos DB 'de istek birimleri ve aktarım hızı
description: Azure Cosmos DB içinde Istek birimi gereksinimlerini belirtme ve tahmin etme hakkında bilgi edinin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6e5d95a47261445e3031f55368f4e2cd8e2830a7
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754858"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB istek birimleri

Azure Cosmos DB, sağladığınız aktarım hızı ve saatlik olarak kullandığınız depolama alanı için ödeme yaparsınız. Azure Cosmos veritabanınız için her zaman yeterli sistem kaynaklarının kullanılabilir olduğundan emin olmak için üretilen iş sağlanmalıdır. [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)'larını karşılamak veya aşmak için yeterli kaynak gereklidir.

Azure Cosmos DB SQL, MongoDB, Cassandra, Gremlin ve Table gibi birçok API 'yi destekler. Her API 'nin kendi veritabanı işlemleri kümesi vardır. Bu işlemler, karmaşık sorgulardan okuma ve yazma işlemleri için basit noktadan aralığıdır. Her veritabanı işlemi, işlemin karmaşıklığına göre sistem kaynaklarını tüketir. 

Tüm veritabanı işlemlerinin maliyeti Azure Cosmos DB normalleştirilerek, *Istek birimleri* (veya ru) tarafından ifade edilir. Saniye başına, aktarım hızı için para birimi olarak düşünebilirsiniz. Saniyedeki ru, Fiyat tabanlı bir para birimidir. Azure Cosmos DB tarafından desteklenen veritabanı işlemlerini gerçekleştirmek için gereken CPU, ıOPS ve bellek gibi sistem kaynaklarını soyutlar. 

1 KB 'lık öğeyi okuma maliyeti 1 Istek birimi (veya 1 RU). Diğer tüm veritabanı işlemlerine aynı şekilde ru kullanılarak maliyet atanır. Azure Cosmos kapsayıcınızda etkileşim kurmak için kullandığınız API 'yi her zaman, maliyetler her zaman RUs ile ölçülür. Veritabanı işleminin yazma, okuma veya sorgulama olup olmadığı, maliyetlerin her zaman RUs cinsinden ölçüldüğü.

Aşağıdaki görüntüde, RUs 'nin üst düzey fikrini gösterilmektedir:

![Veritabanı işlemleri Istek birimlerini tüketir](./media/request-units/request-units.png)

Azure Cosmos DB, kapasiteyi yönetmek ve planlamak için, belirli bir veri kümesi üzerinde belirli bir veritabanı işleminin RUs sayısının belirleyici olmasını sağlar. Herhangi bir veritabanı işlemi tarafından tüketilen ru sayısını izlemek için yanıt üst bilgisini inceleyebilirsiniz. RU ücretlerini ve uygulamanızın aktarım hızı gereksinimlerini [etkileyen faktörleri](request-units.md#request-unit-considerations) anladığınızda, uygulama maliyetinizi etkin bir şekilde çalıştırabilirsiniz.

Uygulamanız için ru sayısını saniyede 100 ru 'lik artışlarla saniye başına sağlayabilirsiniz. Uygulamanızın sağlanan aktarım hızını ölçeklendirmek için dilediğiniz zaman ru sayısını artırabilir veya azaltabilirsiniz. 100 ru 'nin artışlarını veya ölçeğini azaltır. Değişikliklerinizi programlama yoluyla veya Azure portal kullanarak yapabilirsiniz. Saatlik olarak faturalandırılırsınız.

Aktarım hızını iki ayrı granuya temin edebilirsiniz: 

* **Kapsayıcılar**: daha fazla bilgi için bkz. [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md).
* **Veritabanları**: daha fazla bilgi için bkz. [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>İstek birimi değerlendirmeleri

Sağlamak için saniye başına ru sayısını tahmin ederken aşağıdaki faktörleri göz önünde bulundurun:

* **Öğe boyutu**: bir öğenin boyutu arttıkça, öğeyi okumak veya yazmak Için tüketilen Rus sayısı da artar.

* **Öğe dizin oluşturma**: varsayılan olarak, her öğe otomatik olarak dizinlenir. Bir kapsayıcıda bazı öğelerinizin dizinini oluşturma seçeneğini belirlerseniz, daha az RUs kullanılır.

* **Öğe özelliği sayısı**: varsayılan dizin oluşturmanın tüm özelliklerde olduğu varsayıldığında, öğe yazmak Için tüketilen Rus sayısı, öğe özelliği sayısı arttıkça artar.

* **Dizinli Özellikler**: her kapsayıcıda bir dizin ilkesi, varsayılan olarak hangi özelliklerin dizine alınacağını belirler. Yazma işlemlerine yönelik RU tüketimini azaltmak için, dizinlenmiş özelliklerin sayısını sınırlayın.

* **Veri tutarlılığı**: güçlü ve sınırlanmış stalet tutarlılık düzeyleri, diğer gevşek tutarlılık seviyelerine kıyasla okuma işlemleri gerçekleştirirken yaklaşık iki kat daha fazla tüketir.

* **Sorgu desenleri**: bir sorgunun karmaşıklığı, bir işlem için kaç ru tüketildiğini etkiler. Sorgu işlemlerinin maliyetini etkileyen faktörler şunlardır: 
    
    - Sorgu sonuçlarının sayısı
    - Koşulların sayısı
    - Koşulların doğası
    - Kullanıcı tanımlı işlevlerin sayısı
    - Kaynak verilerin boyutu
    - Sonuç kümesinin boyutu
    - Yansıtmalar

  Azure Cosmos DB aynı verilerdeki aynı sorgunun her zaman aynı şekilde yinelenen yürütmeler üzerinde aynı şekilde maliyetlenmesini garanti eder.

* **Betik kullanımı**: sorgularda olduğu gibi, saklı yordamlar ve Tetikleyiciler, gerçekleştirilen işlemlerin karmaşıklığına göre Rus kullanır. Uygulamanızı geliştirirken, her işlemin tükettiği RU kapasitesini daha iyi anlamak için [istek ücreti üst bilgisini](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos kapsayıcılarında ve veritabanlarında işleme sağlama](set-throughput.md)hakkında daha fazla bilgi edinin.
* [Mantıksal bölümler](partition-data.md)hakkında daha fazla bilgi edinin.
* [Sağlanan aktarım hızını genel olarak ölçeklendirme](scaling-throughput.md)hakkında daha fazla bilgi edinin.
* [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md)hakkında bilgi edinin.
* [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md)hakkında bilgi edinin.
* [Bir işlem için istek birimi ücreti bulmayı](find-request-unit-charge.md)öğrenin.
* [Azure Cosmos DB ' de sağlanan üretilen iş maliyetini en uygun hale getirmeyi](optimize-cost-throughput.md)öğrenin.
* [Azure Cosmos DB ' de okuma ve yazma maliyetini en iyileştirmeden](optimize-cost-reads-writes.md)öğrenin.
* [Sorgu maliyetinin Azure Cosmos DB nasıl iyileştirileceği](optimize-cost-queries.md)hakkında bilgi edinin.
* [Verimlilik izlemek için ölçümleri nasıl kullanacağınızı](use-metrics.md)öğrenin.
