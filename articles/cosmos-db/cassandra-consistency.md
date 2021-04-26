---
title: Apache Cassandra ve Azure Cosmos DB tutarlılık düzeyleri
description: Apache Cassandra ve Azure Cosmos DB tutarlılık düzeyleri.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516840"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra ve Azure Cosmos DB Cassandra API tutarlılık düzeyleri
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB aksine Apache Cassandra kesin olarak tanımlanmış tutarlılık garantisi sağlamaz. Bunun yerine Apache Cassandra, yüksek kullanılabilirlik, tutarlılık ve gecikme süresi avantajları sağlamak için bir yazma tutarlılığı düzeyi ve bir okuma tutarlılığı düzeyi sağlar. Azure Cosmos DB Cassandra API kullanırken:

* Apache Cassandra 'ın yazma tutarlılığı düzeyi, Azure Cosmos hesabınızda yapılandırılmış varsayılan tutarlılık düzeyiyle eşleştirilir. Bir yazma işlemi (CL) tutarlılığı, istek başına temelinde değiştirilemez.

* Azure Cosmos DB, Cassandra istemci sürücüsüyle belirtilen okuma tutarlılığı düzeyini, bir okuma isteği üzerinde dinamik olarak yapılandırılmış Azure Cosmos DB tutarlılık düzeylerinden birine dinamik olarak eşler.

## <a name="multi-region-writes-vs-single-region-writes"></a>Çok bölgeli yazma işlemleri tek bölgeli yazma işlemleri

Apache Cassandra veritabanı, varsayılan olarak çok yöneticili bir sistemdir ve okumalar için çok bölgeli çoğaltma içeren tek bölgeli yazma seçeneği sağlamaz. Ancak Azure Cosmos DB, tek bölge veya [çok bölgeli](how-to-multi-master.md) yazma yapılandırmalarına sahip bir anahtar özelliği sağlar. Birden çok bölge arasında tek bir bölge yazma yapılandırması seçebilmenin avantajlarından biri, siteler arası çakışma senaryolarının engelleme ve birden çok bölgede güçlü tutarlılık sağlama seçeneği sunar. 

Tek bölgeli yazmalar sayesinde, [otomatik yük devretme](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)ile bölgeler arasında yüksek kullanılabilirlik düzeyini sürdürmeye devam ederken, güçlü tutarlılık sağlayabilirsiniz. Bu yapılandırmada, istek temelinde son tutarlılığı azaltmak için okuma gecikmesini azaltmak üzere verilerin bulunduğu yere yararlanmaya devam edebilirsiniz. Bu yeteneklere ek olarak, Azure Cosmos DB platform bölge seçme sırasında [bölge yedekliliği](high-availability.md#availability-zone-support) etkinleştirebilme olanağı da sağlar. Bu nedenle, yerel Apache Cassandra 'dan farklı olarak Azure Cosmos DB, büyük bir [denge yelpazesi](consistency-levels.md#rto) ile daha fazla ayrıntı elde etmenizi sağlar.

## <a name="mapping-consistency-levels"></a>Tutarlılık düzeylerini eşleme

Azure Cosmos DB platformu, çoğaltma ve [okal](https://en.wikipedia.org/wiki/CAP_theorem) ve [paclc teoreminin](https://en.wikipedia.org/wiki/PACELC_theorem)tarafından tanımlanan dengelerle ilgili olarak, beş iyi tanımlanmış, iş kullanım örneği yönelimli tutarlılık ayarları kümesi sağlar. Bu yaklaşım Apache Cassandra 'dan önemli ölçüde farklılık gösterdiğinde, [belgelerimizde](consistency-levels.md)Azure Cosmos DB tutarlılık ayarlarını gözden geçirmeniz ve anlamanız için zaman alıp Azure Cosmos DB platformunda tutarlılık ayarlarını anlamak için bu kısa [video](https://www.youtube.com/watch?v=t1--kZjrG-o) kılavuzunu izlemeniz önerilir.

Aşağıdaki tabloda, Cassandra API kullanılırken Apache Cassandra ve Azure Cosmos DB tutarlılık düzeyleri arasındaki olası eşlemeler gösterilmektedir. Bu, tek bölgeye yönelik konfigürasyonları, tek bölge yazma işlemlerini ve çok bölgeli yazmaları gösterir.

> [!NOTE]
> Bunlar tam eşlemeler değildir. Bunun yerine, Apache Cassandra 'ya en yakın anormal şekilde sunuyoruz ve en sağdaki sütundaki tüm nitel farklarını belirsizletik. Yukarıda belirtildiği gibi, Azure Cosmos DB [tutarlılık ayarlarını](consistency-levels.md)gözden geçirmeyi öneririz. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Cassandra tutarlılık hesabı düzeyi eşleme" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Cassandra tutarlılığı dinamik eşleme" lightbox="./media/cassandra-consistency/dynamic.png" :::

Azure Cosmos hesabınız, güçlü tutarlılık dışında bir tutarlılık düzeyiyle yapılandırıldıysa, olasılık açısından *olasılığa sınırlı bir Stalet* (PBS) ölçüsüne bakarak istemcilerinizin iş yükleriniz için güçlü ve tutarlı okumalar alabilir olasılığını fark edebilirsiniz. Bu ölçüm Azure portal kullanıma sunulur, daha fazla bilgi edinmek için bkz. [Izleme olasılığa yönelik sınırlı Stalet (PBS) ölçümü](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Dayalı sınırlanmış stalet, nihai tutarlılığın ne kadar olduğunu gösterir. Bu ölçüm, Azure Cosmos hesabınızda halen yapılandırdığınız tutarlılık düzeyinden daha güçlü bir tutarlılık elde etme konusunda bir fikir sağlar. Diğer bir deyişle, yazma ve okuma bölgelerinin bir birleşimi için kesin olarak tutarlı okuma elde etme olasılığını (milisaniye olarak ölçülür) görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB için genel dağıtım ve tutarlılık düzeyleri hakkında daha fazla bilgi edinin:

* [Genel dağıtıma genel bakış](distribute-data-globally.md)
* [Tutarlılık düzeyine genel bakış](consistency-levels.md)
* [Yüksek kullanılabilirlik](high-availability.md)
