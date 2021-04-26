---
title: Apache Kafka kullanarak verileri PostgreSQL 'ten Azure Cosmos DB Cassandra API hesabına geçirme
description: Kafka Connect kullanarak verileri PostgreSQL 'ten gerçek zamanlı Azure Cosmos DB Cassandra API nasıl eşitleyebileceğinizi öğrenin.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98203074"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Apache Kafka kullanarak verileri PostgreSQL 'ten Azure Cosmos DB Cassandra API hesabına geçirme
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra API, Apache Cassandra üzerinde çalışan kurumsal iş yükleri için, şunlar gibi çeşitli nedenlerle harika bir seçenek haline geldi:

* **Önemli maliyet tasarrufları:** VM 'nin, bant genişliğinin ve ilgili Oracle lisanslarının maliyetini içeren Azure Cosmos DB maliyeti tasarrufu sağlayabilirsiniz. Ayrıca, veri merkezlerini, sunucuları, SSD depolamayı, ağ iletişimini ve elektrik maliyetlerini yönetmeniz gerekmez.

* **Daha iyi ölçeklenebilirlik ve kullanılabilirlik:** Tek hata noktalarını, daha iyi ölçeklenebilirliği ve uygulamalarınız için kullanılabilirliği ortadan kaldırır.

* **Yönetme ve izleme için ek yük yoktur:** Tam olarak yönetilen bir bulut hizmeti olarak Azure Cosmos DB, bir grup ayarı yönetme ve izleme yükünü ortadan kaldırır.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) , ölçeklenebilir ve güvenilir bir şekilde [Apache Kafka](https://kafka.apache.org/) ve diğer sistemler arasında veri akışı için bir platformdur. Raf bağlayıcılarından birkaçını destekler, bu, dış sistemleri Apache Kafka ile tümleştirmede özel kod gerekmediği anlamına gelir.

Bu makalede, [PostgreSQL](https://www.postgresql.org/) gibi bir ilişkisel veritabanındaki kayıtları sürekli olarak [Azure Cosmos DB Cassandra API](cassandra-introduction.md)olarak eşitlemeye yönelik bir veri işlem hattı ayarlamak için bir Kafka bağlayıcıları birleşiminin nasıl kullanılacağı gösterilmektedir.

## <a name="overview"></a>Genel Bakış

İşte bu makalede sunulan uçtan uca akışa yönelik üst düzey genel bakış.

PostgreSQL tablosundaki veriler, Kafka Connect **kaynak** Bağlayıcısı olan [Debezium PostgreSQL Bağlayıcısı](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)kullanılarak Apache Kafka gönderilir. PostgreSQL tablosundaki kayıtları ekleme, güncelleştirme veya silme işlemi olay olarak yakalanacaktır `change data` ve Kafka konularına gönderilir. [DataStax Apache Kafka Bağlayıcısı](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (Kafka Connect **Sink** Connector), ardışık düzenin ikinci bölümünü oluşturur. Kafka konusunun değişiklik verisi olaylarını Azure Cosmos DB Cassandra API tablolarına eşitler.

> [!NOTE]
> DataStax Apache Kafka bağlayıcısının belirli özelliklerinin kullanılması, verileri birden çok tabloya göndermemizi sağlar. Bu örnekte, bağlayıcı değişiklik verisi kayıtlarını farklı sorgu gereksinimlerini destekleyebilen iki Cassandra tablosuna kalıcı hale getirmemize yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Cosmos DB Cassandra API hesabı sağlama](create-cassandra-dotnet.md#create-a-database-account)
* [Doğrulama için csqlsh veya hosted Shell kullanma](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 veya üzeri
* [Docker](https://www.docker.com/) (isteğe bağlı)

## <a name="base-setup"></a>Temel kurulum

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Henüz yapmadıysanız PostgreSQL veritabanını ayarlayın. 

Bu, mevcut bir şirket içi veritabanı olabilir veya bir yerel makinenize [indirip yükleyebilirsiniz](https://www.postgresql.org/download/) . [Docker kapsayıcısı](https://hub.docker.com/_/postgres)kullanmak da mümkündür.

Bir kapsayıcıyı başlatmak için:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

İstemcisini kullanarak PostgreSQL örneğinize bağlanın [`psql`](https://www.postgresql.org/docs/current/app-psql.html) :

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Örnek sipariş bilgilerini depolamak için bir tablo oluşturun:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Azure portal kullanarak, Cassandra keyspace ve demo uygulaması için gereken tabloları oluşturun.

> [!NOTE]
> Aynı anahtar alanını ve tablo adlarını aşağıda gösterildiği gibi kullanın

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Kurulum Apache Kafka 

Bu makalede yerel bir küme kullanılmaktadır, ancak başka bir seçeneği belirleyebilirsiniz. [Kafka indirin](https://kafka.apache.org/downloads), sıkıştırmayı açın, Zookeeper ve Kafka kümesini başlatın.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Kurulum bağlayıcıları

Debezium PostgreSQL ve DataStax Apache Kafka bağlayıcısını yükleme. Debezium PostgreSQL bağlayıcı eklentisi arşivini indirin. Örneğin, bağlayıcının 1.3.0 sürümünü indirmek için (yazma sırasında en son), [Bu bağlantıyı](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz)kullanın. DataStax Apache Kafka bağlayıcısını [Bu bağlantıdan](https://downloads.datastax.com/#akc)indirin.

Bağlayıcı arşivlerini her iki sıkıştırmayı açın ve JAR dosyalarını [Kafka Connect Plugin. Path](https://kafka.apache.org/documentation/#connectconfigs)dosyasına kopyalayın.


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Ayrıntılar için lütfen [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) ve [DataStax](https://docs.datastax.com/en/kafka/doc/) belgelerine bakın.

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Kafka Connect ve başlangıç verileri işlem hattını yapılandırma

### <a name="start-kafka-connect-cluster"></a>Kafka Connect kümesini Başlat

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>PostgreSQL bağlayıcı örneğini Başlat

Bağlayıcı yapılandırmasını (JSON) bir dosya örneğine kaydetme `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

PostgreSQL bağlayıcı örneğini başlatmak için:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Öğesini silmek için şunları kullanabilirsiniz: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Veri ekleme

`orders_info`Tablo, SIPARIŞ kimliği, MÜŞTERI kimliği, şehir vb. gibi sipariş ayrıntılarını içerir. Tabloyu aşağıdaki SQL kullanarak rastgele verilerle doldurun.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Tabloya 10 Kayıt eklemesi gerekir. Aşağıdaki içindeki kayıt sayısını, `generate_series(1, 10)` gereksinimler örneğinizdeki kayıtları eklemek için güncelleştirdiğinizden emin olun `100` , şunu kullanın `generate_series(1, 100)`

Şunları onaylamak için:

```bash
select * from retail.orders_info;
```

Kafka konusunun değişiklik verilerini yakalama olaylarını denetleme

> [!NOTE]
> Konu adının `myserver.retail.orders_info` [bağlayıcı kuralına](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names) göre olduğunu unutmayın.

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Değişiklik verisi olaylarını JSON biçiminde görmeniz gerekir.

### <a name="start-datastax-apache-kafka-connector-instance"></a>DataStax Apache Kafka bağlayıcı örneğini Başlat

Bağlayıcı yapılandırmasını (JSON) bir dosya örneğine kaydedin `cassandra-sink-config.json` ve özellikleri ortamınıza göre güncelleştirin.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Bağlayıcı örneğini başlatmak için:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Bağlayıcının işlem yapması ve PostgreSQL 'den uçtan uca ardışık düzen Azure Cosmos DB çalışır durumda olacaktır.

### <a name="query-azure-cosmos-db"></a>Azure Cosmos DB'yi sorgulama

Azure Cosmos DB Cassandra tablolarını kontrol edin. Deneyebileceğiniz bazı sorgular aşağıda verilmiştir:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

PostgreSQL 'e daha fazla veri eklemeye devam edebilir ve kayıtların Azure Cosmos DB eşitlendiğini doğrulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Kafka Connect kullanarak Apache Kafka ve Azure Cosmos DB Cassandra API tümleştirin](cassandra-kafka-connect.md)
* [Değişiklik verilerini yakalama için Debezium ile Azure Event Hubs 'da (Önizleme) Apache Kafka Connect 'i tümleştirin](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Blitzz kullanarak Oracle 'dan Azure Cosmos DB Cassandra API verileri geçirme](oracle-migrate-cosmos-db-blitzz.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md) 
* [Bölüm anahtarı en iyi uygulamaları](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB kapasite planlayıcısı makalelerini kullanarak ru/s 'Yi tahmin](estimate-ru-with-capacity-planner.md) etme

