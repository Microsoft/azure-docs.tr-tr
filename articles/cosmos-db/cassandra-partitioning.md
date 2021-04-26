---
title: Azure Cosmos DB Cassandra API bölümleniyor
description: Azure Cosmos DB Cassandra API bölümleme hakkında bilgi edinin
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ba615d3e41393afe007238a0fe1e694732ad123e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087647"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API bölümleniyor
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bu makalede bölümlemenin Azure Cosmos DB Cassandra API nasıl çalıştığı açıklanır. 

Cassandra API, bir anahtar alanı içindeki tabloları, uygulamanızın performans ihtiyaçlarını karşılayacak şekilde ölçeklendirmek için bölümleme kullanır. Bölümler, bir tablodaki her kayıtla ilişkili bir bölüm anahtarının değerine göre oluşturulur. Bir bölümdeki tüm kayıtlar aynı bölüm anahtarı değerine sahip. Azure Cosmos DB saydam bir şekilde ve tablonun ölçeklenebilirlik ve performans ihtiyaçlarını etkili bir şekilde karşılamak için bölümlerin fiziksel kaynaklardaki yerleşimini otomatik olarak yönetir. Bir uygulamanın aktarım hızı ve depolama gereksinimleri arttıkça, Azure Cosmos DB verileri daha fazla sayıda fiziksel makine üzerinde taşıdıkça dengeler.

Geliştirici perspektifinden bölümlendirme, yerel [Apache Cassandra](https://cassandra.apache.org/)'da yaptığı gibi Azure Cosmos DB Cassandra API aynı şekilde davranır. Ancak, arka planda bazı farklılıklar vardır. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Apache Cassandra ve Azure Cosmos DB arasındaki farklar

Azure Cosmos DB, bölümlerin depolandığı her makinenin kendisi [fiziksel bölüm](partitioning-overview.md#physical-partitions)olarak adlandırılır. Fiziksel bölüm bir sanal makineye oturum, ayrılmış bir işlem birimi veya fiziksel kaynak kümesi. Bu işlem biriminde depolanan her bölüm, Azure Cosmos DB bir [mantıksal bölüm](partitioning-overview.md#logical-partitions) olarak adlandırılır. Apache Cassandra hakkında zaten bilgi sahibiyseniz, mantıksal bölümleri Cassandra 'daki normal bölümlerin olduğu şekilde düşünebilirsiniz. 

Apache Cassandra, bir bölümde depolanabilecek verilerin boyutuyla ilgili 100 MB 'lik bir sınır önerir. Azure Cosmos DB Cassandra API, mantıksal bölüm başına en fazla 20 GB ve fiziksel bölüm başına en çok 30 veri sağlar. Azure Cosmos DB ' de, Apache Cassandra 'dan farklı olarak, fiziksel bölümde bulunan işlem kapasitesi, [İstek birimleri](request-units.md)adlı tek bir ölçüm kullanılarak ifade edilir ve bu da iş yükünüzü, çekirdekler, bellek veya IOPS yerine saniye başına istek (okuma veya yazma) açısından düşünmenize olanak tanır. Bu, her bir isteğin maliyetini anladıktan sonra Kapasite planlamasını daha basit hale getirir. Her fiziksel bölüm, en fazla 10000 ru işlem kullanabilir. Cassandra API ' deki [elastik ölçekte](manage-scale-cassandra.md) makalemizi okuyarak ölçeklenebilirlik seçenekleri hakkında daha fazla bilgi edinebilirsiniz. 

Azure Cosmos DB, her fiziksel bölüm, bölüm başına en az 4 çoğaltmayla çoğaltma kümesi olarak da bilinen bir çoğaltmalar kümesinden oluşur. Bu, 1. çoğaltma faktörünü ayarlamanın mümkün olduğu Apache Cassandra 'ya karşılık gelen bir yerdir. Ancak, verileri içeren tek düğüm aşağı doğru olursa bu, düşük kullanılabilirliğe yol açar. Cassandra API her zaman yineleme faktörü 4 ' ün (çekirdek 3 ' ü) vardır. Azure Cosmos DB, çoğaltma kümelerini otomatik olarak yönetir, ancak Apache Cassandra 'daki çeşitli araçlar kullanılarak saklanması gerekir. 

Apache Cassandra, Bölüm anahtarlarının karmaları olan bir belirteç kavramıdır. Belirteçler,-2 ^ 63 ile-2 ^ 63-1 arasındaki değerleri içeren bir murmur3 64 bayt karmasını temel alır. Bu Aralık, genellikle Apache Cassandra 'daki "Token Ring" olarak adlandırılır. Belirteç halkası, belirteç aralıklarına dağıtılır ve bu aralıklar yerel Apache Cassandra kümesinde bulunan düğümler arasında bölünür. Azure Cosmos DB için bölümlendirme, farklı bir karma algoritması kullandığından ve daha büyük bir iç belirteç halkası içerdiğinden benzer bir şekilde uygulanır. Ancak harici olarak aynı belirteç aralığını Apache Cassandra ile birlikte kullanıma sunduk, yani,-2 ^ 63-2 ^ 63-1.


## <a name="primary-key"></a>Birincil anahtar

Cassandra API tüm tabloların tanımlı bir olması gerekir `primary key` . Birincil anahtar sözdizimi aşağıda gösterilmiştir:

```shell
column_name cql_type_definition PRIMARY KEY
```

Farklı kullanıcılar için iletileri depolayan bir kullanıcı tablosu oluşturmak istiyoruz:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

Bu tasarımda, `id` alanı birincil anahtar olarak tanımladık. Birincil anahtar, tablodaki kayıt için tanımlayıcı olarak işlev görür ve ayrıca Azure Cosmos DB bölüm anahtarı olarak da kullanılır. Birincil anahtar daha önce açıklanan şekilde tanımlanmışsa, her bölümde yalnızca tek bir kayıt olur. Bu, veritabanına veri yazarken kusursuz bir yatay ve ölçeklenebilir dağıtıma neden olur ve anahtar-değer arama kullanım örnekleri için idealdir. Uygulamanın, okuma performansını en üst düzeye çıkarmak için tablodan verileri okurken birincil anahtar sağlaması gerekir. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="bölümler" border="false":::


## <a name="compound-primary-key"></a>Bileşik birincil anahtar

Apache Cassandra 'nin bir kavramı de vardır  `compound keys` . Bir bileşik birden `primary key` fazla sütundan oluşur; ilk sütun, `partition key` ve diğer tüm sütunlar olur `clustering keys` . Bir için sözdizimi `compound primary key` aşağıda gösterilmiştir:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Yukarıdaki tasarımın değiştirilmesini ve belirli bir kullanıcı için iletileri etkin bir şekilde almayı olanaklı hale getirmek istediğinizi varsayalım:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

Bu tasarımda artık `user` bölüm anahtarı olarak ve `id` kümeleme anahtarı olarak tanımlanıyor. İstediğiniz kadar çok kümeleme anahtarı tanımlayabilirsiniz, ancak her bir değer (veya bir değer birleşimi), aynı bölüme birden çok kaydın eklenmesine yol açacak şekilde benzersiz olmalıdır, örneğin:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Veriler döndürüldüğünde, Apache Cassandra 'da beklendiği gibi kümeleme anahtarına göre sıralanır:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Kümeleme anahtarına göre sıralanmış döndürülen verileri gösteren ekran görüntüsü.":::

Bu şekilde modellenen verilerle, her bölüme Kullanıcı tarafından gruplanmış birden çok kayıt atanabilir. Bu nedenle, `partition key` `user` belirli bir kullanıcıya ait tüm iletileri almak için (Bu örnekte,) tarafından etkin bir şekilde yönlendirilen bir sorgu yayınlarız. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Her bölüme birden çok kaydın nasıl atanagösterdiğini gösteren diyagram, kullanıcıya göre gruplandırılır." border="false":::


## <a name="composite-partition-key"></a>Bileşik bölüm anahtarı

Bileşik bölüm anahtarları temelde bileşik anahtarlarla aynı şekilde çalışır, ancak birden çok sütunu bileşik bölüm anahtarı olarak belirtebilirsiniz. Bileşik bölüm anahtarlarının sözdizimi aşağıda gösterilmiştir:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Örneğin, öğesinin benzersiz birleşiminin `firstname` ve `lastname` bölüm anahtarını nerede oluşturacağından ve `id` kümeleme anahtarı olduğu aşağıdakilere sahip olabilirsiniz:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB bölümünde bölümlendirme ve yatay ölçeklendirme](partitioning-overview.md)hakkında bilgi edinin.
* [Azure Cosmos DB 'de sağlanan aktarım hızı](request-units.md)hakkında bilgi edinin.
* [Azure Cosmos DB 'de küresel dağıtım](distribute-data-globally.md)hakkında bilgi edinin.
