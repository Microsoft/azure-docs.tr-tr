---
title: Değişiklik verilerini yakalama için Azure Event Hubs üzerinde Debezium ile tümleştirme Apache Kafka bağlama
description: Bu makalede, Kafka için Azure Event Hubs Debezium kullanımı hakkında bilgi sağlanır.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 01/06/2021
ms.openlocfilehash: 0ad1df23e71e652f7d380ffbabb542b81954e038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935181"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-with-debezium-for-change-data-capture"></a>Değişiklik verilerini yakalama için Debezium ile Azure Event Hubs 'de Apache Kafka Connect desteğini tümleştirin

**Değişiklik verilerini yakalama (CDC)** , oluşturma, güncelleştirme ve silme işlemlerine yanıt olarak veritabanı tablolarındaki satır düzeyi değişiklikleri izlemek için kullanılan bir tekniktir. [Debezium](https://debezium.io/) , farklı veritabanlarında bulunan değişiklik verilerini yakalama özelliklerini (örneğin, [PostgreSQL içinde mantıksal kod çözme](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)) oluşturan, dağıtılmış bir platformdur. Veritabanı tabloları içindeki satır düzeyi değişikliklere dokunarak [Kafka Connect bağlayıcıları](https://debezium.io/documentation/reference/1.2/connectors/index.html) kümesi sağlar ve bunları [Apache Kafka](https://kafka.apache.org/)' a gönderilen olay akışlarına dönüştürür.

> [!WARNING]
> Apache Kafka Connect Framework 'ün yanı sıra, Debezium platformu ve bağlayıcıları **Microsoft Azure aracılığıyla ürün desteği için uygun değildir**.
>
> Apache Kafka Connect, dinamik yapılandırmasının, aksi takdirde sınırsız saklama ile sıkıştırılmış konularda tutulmasını kabul eder. Azure Event Hubs, [bir aracı özelliği olarak sıkıştırmayı uygulamaz](event-hubs-federation-overview.md#log-projections) ve Azure Event Hubs 'in, uzun süreli bir veri veya yapılandırma deposu olmayan gerçek zamanlı bir olay akışı altyapısı olduğu prensip.
>
> Apache Kafka projesi bu rolleri karıştırmaya rahat olabileceğinden, Azure bu bilgilerin uygun bir veritabanı veya yapılandırma deposunda en iyi şekilde yönetildiğini düşünmektedir.
>
> Birçok Apache Kafka Connect senaryosu işlevsel olacaktır, ancak Apache Kafka ve Azure Event Hubs ' saklama modelleri arasındaki bu kavramsal farklılıklar bazı yapılandırmaların beklendiği gibi çalışmamasına neden olabilir. 

Bu öğretici, Azure 'da Azure [Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (Kafka için), [PostgreSQL için Azure DB](../postgresql/overview.md) ve debezium kullanarak Azure 'da değişiklik verilerini yakalama tabanlı sistem ayarlama konusunda size kılavuzluk eder. Azure Event Hubs PostgreSQL 'ten Kafka konularına veritabanı değişiklikleri akışı için [Debezium PostgreSQL bağlayıcısını](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) kullanır

> [!NOTE]
> Bu makalede, Microsoft tarafından kullanılmayan bir terim olan *beyaz liste* teriminin başvuruları yer almaktadır. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Event Hubs ad alanı oluşturma
> * PostgreSQL için Azure veritabanı 'nı ayarlama ve yapılandırma
> * Kafka Connect 'i Debezium PostgreSQL Bağlayıcısı ile yapılandırma ve çalıştırma
> * Test değişiklik verilerini yakalama
> * Seçim Bağlayıcı ile değişiklik verileri olaylarını kullanma `FileStreamSink`

## <a name="pre-requisites"></a>Ön koşullar
Bu izlenecek adımları gerçekleştirmek için şunları yapmanız gerekir:

- Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- Linux/MacOS
- Kafka sürümü (sürüm 1.1.1, Scala sürüm 2.11), [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)'da sağlanır
- [Apache Kafka için Event Hubs](./event-hubs-for-kafka-ecosystem-overview.md) giriş makalesini okuyun

## <a name="create-an-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma
Herhangi bir Event Hubs hizmetinden göndermek ve almak için Event Hubs ad alanı gereklidir. Ad alanı ve Olay Hub 'ı oluşturma yönergeleri için bkz. bir [Olay Hub 'ı oluşturma](event-hubs-create.md) . Daha sonra kullanmak üzere Event Hubs bağlantı dizesini ve tam etki alanı adını (FQDN) alın. Yönergeler için bkz. [Event Hubs bağlantı dizesi alma](event-hubs-get-connection-string.md). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>PostgreSQL için Azure veritabanı 'nı ayarlama ve yapılandırma
[PostgreSQL Için Azure veritabanı](../postgresql/overview.md) , açık kaynak PostgreSQL veritabanı altyapısının topluluk sürümünü temel alan ilişkisel bir veritabanı hizmetidir ve iki dağıtım seçeneklerinde mevcuttur: tek sunucu ve hiper ölçek (Citus). Azure portal kullanarak PostgreSQL için Azure veritabanı sunucusu oluşturmak için [Bu yönergeleri izleyin](../postgresql/quickstart-create-server-database-portal.md) . 

## <a name="setup-and-run-kafka-connect"></a>Kurulum ve çalıştırma Kafka Connect
Bu bölümde aşağıdaki konular ele alınacaktır:

- Debezium bağlayıcı yüklemesi
- Event Hubs için Kafka Connect yapılandırma
- Debezium bağlayıcı ile Kafka Connect kümesi başlatma

### <a name="download-and-setup-debezium-connector"></a>Debezium bağlayıcısını indirme ve kurma
Bağlayıcıyı indirmek ve ayarlamak için lütfen [Debezium belgelerindeki](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) en son yönergeleri izleyin.

- Bağlayıcının eklenti arşivini indirin. Örneğin, bağlayıcının sürümünü indirmek için `1.2.0` Bu bağlantıyı kullanın- https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- JAR dosyalarını ayıklayın ve [Kafka Connect Plugin. Path](https://kafka.apache.org/documentation/#connectconfigs)dosyasına kopyalayın.


### <a name="configure-kafka-connect-for-event-hubs"></a>Event Hubs için Kafka Connect'i yapılandırma
Kafka Connect aktarım hızını Kafka'dan Event Hubs'a yeniden yönlendirmek için çok az yeniden yapılandırma gerekir.  Aşağıdaki `connect-distributed.properties` örneğinde, Event Hubs'da Kafka uç noktasıyla kimlik doğrulaması yapmak ve iletişim kurmak için Connect'in nasıl yapılandırılacağı gösterilir:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`Event Hubs ad alanınız için bağlantı dizesiyle değiştirin. Bağlantı dizesini alma hakkında yönergeler için bkz. [Event Hubs bağlantı dizesi alma](event-hubs-get-connection-string.md). Örnek bir yapılandırma aşağıda verilmiştir: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>Kafka Connect'i çalıştırma
Bu adımda, bir Kafka Connect çalışanı dağıtılmış modda yerel olarak başlatılır ve küme durumunu korumak için Event Hubs kullanılır.

1. Yukarıdaki `connect-distributed.properties` dosyasını yerel olarak kaydedin.  Küme ayracı içindeki tüm değerleri değiştirdiğinizden emin olun.
2. Makinenizde Kafka sürümünün konumuna gidin.
3. Çalıştırın `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` ve kümenin başlamasını bekleyin.

> [!NOTE]
> Kafka Connect, Kafka AdminClient API 'sini kullanarak, sıkıştırma de dahil olmak üzere önerilen yapılandırmalara sahip konuları otomatik olarak oluşturur. Azure portalında ad alanına hızla göz attığınızda, Connect çalışanı iç konusunun otomatik olarak oluşturulduğu ortaya çıkar.
>
> Kafka Connect iç konuları **sıkıştırmayı kullanmalıdır**.  Event Hubs ekibi, iç bağlantı konuları yanlış yapılandırılmışsa yanlış yapılandırmaların düzeltilmesinden sorumludur.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Debezium PostgreSQL kaynak bağlayıcısını yapılandırma ve başlatma

`pg-source-connector.json`PostgreSQL için bir yapılandırma dosyası () oluşturun kaynak Bağlayıcısı-değerleri Azure PostgreSQL örneğinize göre değiştirin.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` öznitelik, izlenmekte olan belirli bir PostgreSQL veritabanı sunucusu/kümesi için ad alanı tanımlayan ve sağlayan bir mantıksal addır. Ayrıntılı bilgi için, [Debezium belgelerini](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name) denetleyin

Bağlayıcının bir örneğini oluşturmak için Kafka Connect REST API uç noktasını kullanın:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Bağlayıcının durumunu denetlemek için:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Test değişiklik verilerini yakalama
Değişiklik verilerini yakalama eylemini görmek için Azure PostgreSQL veritabanında kayıt oluşturmanız/güncelleştirmeniz/silmeniz gerekir.

Azure PostgreSQL veritabanınıza bağlanarak başlayın (aşağıdaki örnekte [psql](https://www.postgresql.org/docs/12/app-psql.html)kullanılır)

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Tablo oluşturma ve kayıt ekleme**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

Bağlayıcının işlem yapması ve değişiklik verileri olaylarını bir Event Hubs konusuna, bu `my-server.public.todos` değer olarak sahip olduğunuzu varsayarak `my-server` `database.server.name` ve `public.todos` değişiklikleri izlemekte olduğunuz (her `table.whitelist` yapılandırma için)

**Event Hubs konuyu denetle**

Her şeyin beklendiği gibi çalıştığından emin olmak için konunun içeriğine introspect bakalım. Aşağıdaki örnek kullanılmıştır [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , ancak [burada listelenen seçeneklerden birini kullanarak da bir tüketici oluşturabilirsiniz](apache-kafka-developer-guide.md)

Aşağıdaki içeriğe sahip adlı bir dosya oluşturun `kafkacat.conf` :

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> `metadata.broker.list` `sasl.password` Event Hubs bilgi başına ' de güncelleştirme ve öznitelikler `kafkacat.conf` . 

Farklı bir terminalde bir tüketici başlatın:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Tabloya yeni eklediğiniz satırlara yanıt olarak, PostgreSQL içinde oluşturulan değişiklik verisi olaylarını temsil eden JSON yüklerini görmeniz gerekir `todos` . Yükün bir parçacığı aşağıda verilmiştir:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

Olay, `payload` `schema` (breçekimi için atlanmış) ile birlikte oluşur. `payload`Bölümünde, oluşturma işleminin () nasıl temsil ettiğini ve `"op": "c"` `"before": null` Yeni bir satır olduğu anlamına gelir `INSERT` , `after` satırdaki sütunlar için değer sağlar, `source` Bu olayın çekildiği yer olan PostgreSQL örnek meta verilerini sağlar.

Güncelleştirme veya silme işlemleriyle aynı zamanda ve değişiklik verileri olaylarını introspect aynı şekilde deneyebilirsiniz. Örneğin, görev durumunu güncelleştirmek için `configure and install connector` ( `id` olduğu varsayılarak `3` ):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Seçim FileStreamSink bağlayıcısını yükler
Tüm `todos` tablo değişikliklerinin Event Hubs konu başlığında yakalandığına göre, bu olayları kullanmak Için FileStreamSink bağlayıcısını (varsayılan olarak Kafka Connect 'te kullanılabilir) kullanacağız.

Bağlayıcı için bir yapılandırma dosyası ( `file-sink-connector.json` ) oluşturun- `file` özniteliği dosya sisteminize göre değiştirin

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Bağlayıcıyı oluşturmak ve durumunu denetlemek için:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Veritabanı kayıtlarını Ekle/güncelleştir/Sil ve yapılandırılan çıkış havuzu dosyasındaki kayıtları izle:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Temizleme
Kafka Connect, Connect kümesi devre dışı bırakıldıktan sonra bile kalıcı olan yapılandırmaları, uzaklıkları ve durumları depolamak için Event Hub konuları oluşturur. Bu kalıcılık istenen bir şey değilse, bu konuların silinmesi önerilir. Ayrıca, `my-server.public.todos` Bu izlenecek yol boyunca oluşturulan olay hub 'ını silmek isteyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Kafka için Event Hubs hakkında daha fazla bilgi için aşağıdaki makalelere bakın:  

- [Bir olay hub'ında Kafka aracısı yansıtma](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark'ı bir olay hub'ına bağlama](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink'i bir olay hub'ına bağlama](event-hubs-kafka-flink-tutorial.md)
- [GitHub'ımızdaki örnekleri inceleme](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Streams’i bir olay hub’ına bağlama](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs için Apache Kafka Geliştirici Kılavuzu](apache-kafka-developer-guide.md)