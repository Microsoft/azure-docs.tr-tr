---
title: Dış AKıŞ oluşturma (Transact-SQL)-Azure SQL Edge
description: Azure SQL Edge 'de dış AKıŞ oluşturma açıklaması hakkında bilgi edinin
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659231"
---
# <a name="create-external-stream-transact-sql"></a>Dış AKıŞ oluşturma (Transact-SQL)

Dış AKıŞ nesnesinin hem giriş hem de çıkış akışının iki amacı vardır. Azure Olay Hub 'ı, Azure IoT Hub (veya Edge hub) veya Kafka gibi olay alma hizmetlerinden gelen akış verilerini sorgulamak için bir giriş olarak veya bir akış sorgusundan sonuçların nerede ve nasıl depolanacağını belirtmek için bir çıktı olarak kullanılabilir.

Ayrıca, bir dış AKıŞ, Olay Hub 'ı veya blob depolama gibi hizmetler için hem çıkış hem de giriş olarak belirtilebilir ve oluşturulabilir. Bu, akış sorgusunun çıkış olarak dış akışa ve girdi olarak aynı dış akıştan okuduğu başka bir akış sorgusuna neden olduğu zincirleme senaryolarına olanak sağlar.

Azure SQL Edge Şu anda yalnızca akış girişleri ve çıkışları olarak aşağıdaki veri kaynaklarını destekler.

| Veri kaynağı türü | Girdi | Çıktı | Description |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub 'ı | E | E | Bir Azure IoT Edge hub 'ına akış verilerini okumak ve yazmak için veri kaynağı. Daha fazla bilgi için bkz. [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Veritabanı | N | E | SQL veritabanına akış verileri yazmak için veri kaynağı bağlantısı. Veritabanı, Azure SQL Edge 'deki bir yerel veritabanı veya SQL Server ya da Azure SQL veritabanı 'ndaki uzak bir veritabanı olabilir.|
| Kafka | E | N | Bir Kafka konusunun akış verilerini okumak için veri kaynağı. Kafka desteği, Azure SQL Edge 'in ARM64 sürümünde kullanılamaz.|



## <a name="syntax"></a>Söz dizimi

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>Bağımsız değişkenler

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **Konum**: veri kaynağındaki gerçek verilerin veya konumun adını belirtir. 
   - Edge hub veya Kafka Stream nesneleri için konum, okuma veya yazma için uç hub veya Kafka konusunun adını belirtir.
   - SQL Stream nesneleri için (SQL Server, Azure SQL veritabanı veya Azure SQL Edge) konumu, tablonun adını belirtir. Akış, hedef tabloyla aynı veritabanında ve şemada oluşturulduysa yalnızca tablo adı yeterli olur. Aksi halde tablo adını tam olarak nitelemeniz gerekir (<veritabanı_adı. schema_name. table_name).
   - Azure Blob depolama akışı nesne konumu, blob kapsayıcısı içinde kullanılacak yol deseninin anlamına gelir. Bu özellik hakkında daha fazla bilgi için bkz. (/makalenles/Stream-Analtics/Stream-Analtics-define-Outputs.MD # blob-Storage-and-Azure-Data-Lake-Gen2)

- **INPUT_OPTIONS**: Kafka gibi hizmetler için anahtar-değer çiftleri olarak seçenekleri belirtin, akış sorgularının girdileri olan IoT Edge hub
    - BÖLÜMLER: bir konu için tanımlanan bölüm sayısı. Kullanılabilecek en fazla bölüm sayısı 32 ile sınırlıdır.
      - Kafka giriş akışları için geçerlidir
    - CONSUMER_GROUP: olay ve IoT Hub 'Ları, bir tüketici grubundaki okuyucu sayısını sınırlar (5 ' e kadar). Bu alanı boş bırakmak, ' $Default ' tüketici grubunu kullanır.
      - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.  
    - TIME_POLICY: geç olaylar veya sipariş dışı olaylar tolerans değerlerini geçilişinde olayların mi yoksa olay saatinin mi ayarlanacağını açıklar.
      - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.
    - LATE_EVENT_TOLERANCE: kabul edilebilir en fazla gecikme süresi. Gecikme, olayın zaman damgası ve sistem saati arasındaki farkı temsil eder.
      - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.
    - OUT_OF_ORDER_EVENT_TOLERANCE: olaylar, akış sorgusuna yapılan yolculuğa kadar bir sıra sonra gelebilir. Bu olaylar olduğu gibi kabul edilebilir veya bir süre sonra yeniden sıralamak üzere bir ayarlama dönemi için duraklamayı tercih edebilirsiniz.
      - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.
        
- **OUTPUT_OPTIONS**:, akış sorgularına çıkış yapan desteklenen hizmetler için anahtar-değer çiftleri olarak seçenekleri belirtin 
  - REJECT_POLICY: BıRAK | Veri dönüştürme hataları oluştuğunda veri hatası işleme ilkeleri türler yeniden deneyin. 
    - Desteklenen tüm çıktılar için geçerlidir 
  - MINIMUM_ROWS:  
    Bir çıkışa yazılan toplu iş başına gereken minimum satır. Parquet için her Batch yeni bir dosya oluşturur. 
    - Desteklenen tüm çıktılar için geçerlidir 
  - MAXIMUM_TIME:  
    Toplu iş başına dakika cinsinden maksimum bekleme süresi. Bu süreden sonra, en düşük satır gereksinimi karşılanmasa bile toplu iş çıktıya yazılır. 
    - Desteklenen tüm çıktılar için geçerlidir 
  - PARTITION_KEY_COLUMN:  
    Bölüm anahtarı için kullanılan sütun. 
    - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.
  - PROPERTY_COLUMNS:  
    İstenirse, iletilere özel özellikler olarak eklenecek çıkış sütunlarının adlarının virgülle ayrılmış bir listesi.  
    - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir. 
  - SYSTEM_PROPERTY_COLUMNS:  
    Service Bus iletilerde doldurulacak sistem özellik adlarının ve çıktı sütunlarının ad/değer çiftlerinin bir JSON biçimli koleksiyonu. örn. {"MessageID": "Sütun1", "PartitionKey": "Sütun2"} 
    - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir. 
  - PARTITION_KEY:  
    Bölüm anahtarını içeren çıkış sütununun adı. Bölüm anahtarı, bir varlığın birincil anahtarının ilk kısmını oluşturan belirli bir tablo içindeki bölüm için benzersiz bir tanımlayıcıdır. Boyutu 1 KB 'a kadar olabilecek bir dize değeridir. 
    - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.
  - ROW_KEY:  
    Satır anahtarını içeren çıkış sütununun adı. Satır anahtarı, belirli bir bölüm içindeki bir varlık için benzersiz bir tanımlayıcıdır. Bir varlığın birincil anahtarının ikinci bölümünü oluşturur. Satır anahtarı, boyutu 1 KB 'a kadar olabilecek bir dize değeridir. 
    - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.
  - BATCH_SIZE:  
    Bu, en fazla 100 kayda gidebileceği tablo depolaması için işlem sayısını temsil eder. Azure Işlevleri için bu, çağrı başına işleve gönderilen bayt cinsinden toplu iş boyutunu temsil eder-varsayılan değer 256 kB 'dir. 
    - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir. 
  - MAXIMUM_BATCH_COUNT:  
    Azure işlevi için çağrı başına işleve gönderilen en fazla olay sayısı-varsayılan 100 ' dir. SQL veritabanı için, bu, her toplu ekleme işlemi ile gönderilen en fazla kayıt sayısını temsil eder-varsayılan değer 10.000 ' dir. 
    - Tüm SQL tabanlı çıkışlar için geçerlidir 
  - STAGING_AREA: dış VERI kaynağı nesnesi BLOB depolama alanına, Azure SYNAPSE Analytics 'e yönelik yüksek performanslı veri alımı için hazırlama alanı 
    - Gelecekteki kullanım için ayrılmıştır. Azure SQL Edge için geçerlidir.


## <a name="examples"></a>Örnekler

### <a name="example-1---edgehub"></a>Örnek 1-EdgeHub

Tür: giriş veya çıkış<br>

Söz dizimi:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Örnek 2-Azure SQL veritabanı, Azure SQL Edge, SQL Server

Tür: çıkış<br>

Söz dizimi:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>Örnek 3-Kafka

Tür: giriş<br>

Söz dizimi:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>Ayrıca bkz.

- [Dış akışı bırak (Transact-SQL)](drop-external-stream-transact-sql.md)
