---
title: Azure Cosmos DB bölümlendirme ve yatay ölçekleme
description: Bölümlemenin Azure Cosmos DB nasıl çalıştığına, bölümleme ve bölüm anahtarlarının nasıl yapılandırılacağını ve uygulamanız için doğru bölüm anahtarının nasıl seçeceğinizi öğrenin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754919"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB bölümlendirme ve yatay ölçekleme

Bu makalede Azure Cosmos DB içindeki fiziksel ve mantıksal bölümler açıklanmaktadır. Ayrıca, ölçekleme ve bölümleme için en iyi yöntemleri açıklar. 

## <a name="logical-partitions"></a>Mantıksal bölümler

Mantıksal bir bölüm, aynı bölüm anahtarına sahip bir öğe kümesinden oluşur. Örneğin, tüm öğelerin `City` özellik içerdiği bir kapsayıcıda, kapsayıcının bölüm anahtarı olarak `City` kullanabilirsiniz. @No__t_1, `Paris` ve `NYC` gibi `City` için belirli değerlere sahip öğe grupları, farklı mantıksal bölümler oluşturur. Temel alınan veriler silindiğinde bir bölümü silme konusunda endişelenmeniz gerekmez.

Azure Cosmos DB, kapsayıcı temel ölçeklenebilirlik birimidir. Kapsayıcıya eklenen veriler ve kapsayıcıda sağladığınız aktarım hızı, mantıksal bölümlerin bir kümesi genelinde otomatik olarak (yatay olarak) bölümlenir. Veri ve aktarım hızı, Azure Cosmos kapsayıcısı için belirttiğiniz bölüm anahtarına göre bölümlendirilir. Daha fazla bilgi için bkz. [Azure Cosmos kapsayıcısı oluşturma](how-to-create-container.md).

Mantıksal bir bölüm ayrıca veritabanı işlemlerinin kapsamını tanımlar. Bir mantıksal bölüm içindeki öğeleri, [anlık görüntü yalıtımıyla bir işlem](database-transactions-optimistic-concurrency.md)kullanarak güncelleştirebilirsiniz. Bir kapsayıcıya yeni öğeler eklendiğinde, yeni mantıksal bölümler sistem tarafından saydam olarak oluşturulur.

## <a name="physical-partitions"></a>Fiziksel bölümler

Azure Cosmos kapsayıcısı, çok sayıda mantıksal bölüm arasında veri ve aktarım hızı dağıtarak ölçeklendirilir. Dahili olarak, bir veya daha fazla mantıksal bölüm, [*çoğaltma kümesi*](global-dist-under-the-hood.md)olarak da adlandırılan bir çoğaltmalar kümesinden oluşan bir fiziksel bölüme eşlenir. Her çoğaltma kümesi, Azure Cosmos veritabanı altyapısının bir örneğini barındırır. Bir çoğaltma kümesi, fiziksel bölümde depolanan verilerin dayanıklı, yüksek oranda kullanılabilir ve tutarlı olmasını sağlar. Fiziksel bir bölüm, en fazla depolama ve istek birimi (ru) miktarını destekler. Fiziksel bölümü oluşturan her çoğaltma, bölümün depolama kotasını devralır. Fiziksel bölümün tüm çoğaltmaları, fiziksel bölüme ayrılan üretilen işi topluca destekler. 

Aşağıdaki görüntüde, mantıksal bölümlerin küresel olarak dağıtılan fiziksel bölümlerle nasıl eşlendiği gösterilmektedir:

![Azure Cosmos DB bölümleme gösteren bir görüntü](./media/partition-data/logical-partitions.png)

Bir kapsayıcı için sağlanan aktarım hızı fiziksel bölümler arasında eşit olarak bölünür. İşleme isteklerini eşit olarak dağıtmayan bölüm anahtarı tasarımı "sık erişimli" bölümler oluşturabilir. Sık kullanılan bölümler, sağlanan verimlilik ve daha yüksek maliyetler için hız sınırlaması ve verimsiz bir şekilde kullanılmasına neden olur.

Mantıksal bölümlerin aksine, fiziksel bölümler sistemin dahili bir uygulamasıdır. Fiziksel bölümlerin boyutunu, yerleşimini veya sayısını kontrol edebilirsiniz ve mantıksal bölümler ile fiziksel bölümler arasındaki eşlemeyi kontrol edebilirsiniz. Ancak, [doğru mantıksal bölüm anahtarını seçerek](partitioning-overview.md#choose-partitionkey)mantıksal bölümlerin sayısını ve verilerin, iş yükünün ve aktarım hızının dağıtımını denetleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Bölüm anahtarı seçme](partitioning-overview.md#choose-partitionkey)hakkında bilgi edinin.
* [Azure Cosmos DB 'de sağlanan aktarım hızı](request-units.md)hakkında bilgi edinin.
* [Azure Cosmos DB 'de küresel dağıtım](distribute-data-globally.md)hakkında bilgi edinin.
* [Azure Cosmos kapsayıcısında üretilen iş sağlama](how-to-provision-container-throughput.md)hakkında bilgi edinin.
* [Azure Cosmos veritabanında üretilen iş sağlama](how-to-provision-database-throughput.md)hakkında bilgi edinin.
