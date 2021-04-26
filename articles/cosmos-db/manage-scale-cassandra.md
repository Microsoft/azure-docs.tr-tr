---
title: Azure Cosmos DB Cassandra API ölçek ile esnek
description: Bir Azure Cosmos DB Cassandra API hesabı ve bunların avantajları/dezavantajlarını ölçeklendirmeye yönelik seçenekler hakkında bilgi edinin
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340966"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Azure Cosmos DB Cassandra API bir hesabı ölçeklendirin
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra için Azure Cosmos DB API 'sinin elastik yapısını keşfetmeye yönelik çeşitli seçenekler vardır. Azure Cosmos DB etkin bir şekilde ölçeklendirmenin nasıl yapılacağını anlamak için, sisteminizdeki performans taleplerini hesaba eklemek üzere doğru istek birimi (RU/sn) miktarına nasıl sağlanacağını anlamak önemlidir. İstek birimleri hakkında daha fazla bilgi için bkz. [İstek birimleri](request-units.md) makalesi. 

Cassandra API için, [.net ve Java SDK](./find-request-unit-charge-cassandra.md)'larını kullanarak tekil sorgular Için istek birimi ücreti alabilirsiniz. Bu, hizmette sağlamanız gereken RU/sn miktarını belirlemede yararlı olacaktır.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Veritabanı işlemleri Istek birimlerini tüketir" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>İşleme hızı sınırlaması (429 hata)

Azure Cosmos DB, istemciler sağladığınız miktardan daha fazla kaynak (RU/sn) kullanıyorsa, oran-Limited (429) hata döndürür. Azure Cosmos DB Cassandra API, bu özel durumları Cassandra Native protokolünde aşırı yüklenmiş hatalara dönüştürür. 

Sisteminiz gecikme süresine duyarlı değilse, yeniden denemeler kullanılarak üretilen iş hızı oranını işlemek yeterli olabilir. Java 'da [Cassandra yeniden deneme ilkesi](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) için [Azure Cosmos DB uzantısını](https://github.com/Azure/azure-cosmos-cassandra-extensions) kullanarak nasıl saydam bir şekilde işleneceğini nasıl işleyeceğinizi gösteren [Java kod örneğine](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) bakın. Aynı zamanda [Spark uzantısını](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) , hız sınırını işlemek için de kullanabilirsiniz.

## <a name="manage-scaling"></a>Ölçeklendirmeyi Yönet

Gecikme süresini en aza indirmek isterseniz, Cassandra API ölçek ve sağlama aktarım hızını (ru) yönetmek için kullanabileceğiniz bir seçenek yelpazesi vardır:

* [Azure portal kullanarak el ile](#use-azure-portal)
* [Denetim düzlemi özelliklerini kullanarak program aracılığıyla](#use-control-plane)
* [Belirli bir SDK ile CQL komutlarını kullanarak programlama yoluyla](#use-cql-queries)
* [Otomatik ölçeklendirme kullanarak dinamik olarak](#use-autoscale)

Aşağıdaki bölümlerde her yaklaşımın avantajları ve dezavantajları açıklanmaktadır. Daha sonra, çözümünüzün ölçeklendirme ihtiyaçlarını, genel maliyet ve gereksinimlerinize yönelik verimlilik ihtiyaçlarını dengelemek için en iyi stratejiye karar verebilirsiniz.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Azure portal kullanın

Azure Cosmos DB Cassandra API hesabındaki kaynakları Azure portal kullanarak ölçeklendirebilirsiniz. Daha fazla bilgi edinmek için [kapsayıcılar ve veritabanlarında üretilen Iş sağlama](set-throughput.md)başlıklı makaleye bakın. Bu makalede, Azure portal [veritabanı](set-throughput.md#set-throughput-on-a-database) ya da [kapsayıcı](set-throughput.md#set-throughput-on-a-container) düzeyinde üretilen işi ayarlamanın göreli avantajları açıklanmaktadır. Bu makalelerde bahsedilen "veritabanı" ve "kapsayıcı" terimleri, Cassandra API için sırasıyla "keyspace" ve "Table" ile eşlenir.

Bu yöntemin avantajı, veritabanında üretilen iş kapasitesini yönetmenin kolay bir anahtar yoludur. Öte yandan, büyük bir deyişle, ölçeklendirmeye yönelik yaklaşımınızın her ikisi de düşük maliyetli ve yüksek performanslı bir otomasyon düzeyinin olması gerekebilir. Sonraki bölümlerde ilgili senaryolar ve yöntemler açıklanmaktadır.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Denetim düzlemi 'ni kullanma

Cassandra için Azure Cosmos DB API 'SI, çeşitli denetim düzlemi özelliklerimizi kullanarak aktarım hızını programlı bir şekilde ayarlama yeteneği sağlar. Rehberlik ve örnekler için [Azure Resource Manager](./templates-samples-cassandra.md), [POWERSHELL](powershell-samples.md)ve [Azure CLI](cli-samples.md) makalelerine bakın.

Bu yöntemin avantajı, yoğun etkinlik veya düşük etkinlik dönemlerinde hesaba bir Zamanlayıcı temelinde kaynakların ölçeğini artırma veya azaltma işlemlerini otomatikleştirebileceğiniz bir yöntemdir. Azure Işlevleri ve PowerShell kullanarak bunu [gerçekleştirmek için](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) örneğimize göz atın.

Bu yaklaşımdan olumsuz bir dezavantajı, öngörülemeyen değişiklik ölçeği ihtiyaçlarına gerçek zamanlı olarak yanıt veremeyebilirsiniz. Bunun yerine, sisteminizde uygulama bağlamından, istemci/SDK düzeyinde veya [Otomatik ölçeklendirme](provision-throughput-autoscale.md)kullanmanız gerekebilir.

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Belirli bir SDK ile CQL sorgularını kullanma

Verilen veritabanı veya kapsayıcı için [CQL alter komutlarını](cassandra-support.md#keyspace-and-table-options) yürüterek sistemi kodla dinamik olarak ölçeklendirebilirsiniz.

Bu yaklaşımın avantajı, ölçek ihtiyaçlarına dinamik olarak ve uygulamanıza uygun özel bir şekilde yanıt vermenize olanak tanır. Bu yaklaşım sayesinde standart RU/s ücretlerinden ve oranlarından yararlanmaya devam edebilirsiniz. Sisteminizin ölçek ihtiyacı genellikle tahmin edilebilir ise (%70 veya daha fazla), CQL ile SDK kullanılması, otomatik ölçeklendirmeyi kullanmaktan farklı şekilde Otomatik ölçeklendirmeye yönelik daha düşük maliyetli bir yöntem olabilir. Bu yaklaşımın dezavantajı, hız sınırlandırma gecikme süresini arttırabilirken yeniden denemeler uygulamak için oldukça karmaşık olabilir.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Otomatik ölçeklendirme sağlanan aktarım hızını kullan

Standart (el ile) veya üretilen iş sağlamaya yönelik programlama yöntemine ek olarak, Azure Cosmos kapsayıcılarını de otomatik ölçeklendirme sağlanan aktarım hızına yapılandırabilirsiniz. Otomatik ölçeklendirme, SLA 'Lara ödün vermeden belirtilen RU aralıklarında tüketim gereksinimlerinize otomatik olarak ve anında ölçeklenecektir. Daha fazla bilgi edinmek için [Otomatik ölçeklendirme makalesinde Azure Cosmos kapsayıcıları ve veritabanları oluşturma](provision-throughput-autoscale.md) makalesine bakın.

Bu yaklaşımın avantajı, sisteminizdeki ölçekleme ihtiyaçlarını yönetmenin en kolay yoludur. **YAPıLANDıRıLAN ru aralıkları içinde** hız sınırlaması uygulamaz. Dezavantajı, sisteminizdeki ölçekleme ihtiyaçları tahmin edilebilir ise, otomatik ölçeklendirme, yukarıda bahsedilen beslenme denetim düzlemi veya SDK düzeyi yaklaşımlarını kullanmaktan daha az uygun maliyetli bir yol olabilir.

CQL kullanarak otomatik ölçeklendirme için maksimum üretilen iş (ru) ayarlamak veya değiştirmek için aşağıdakileri kullanın (anahtar alanı/tablo adını uygun şekilde değiştirin):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Sonraki adımlar

- Java uygulaması kullanarak [Cassandra API hesabı, veritabanı ve tablo oluşturmaya](create-cassandra-api-account-java.md) başlama