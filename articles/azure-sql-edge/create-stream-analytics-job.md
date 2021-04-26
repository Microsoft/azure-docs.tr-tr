---
title: Azure SQL Edge 'de T-SQL akış işi oluşturma
description: Azure SQL Edge 'de Stream Analytics işleri oluşturma hakkında bilgi edinin.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 97189fd7a232c2467981b23dc20da51ebef08252
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656351"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Azure SQL Edge 'de veri akışı işi oluşturma 

Bu makalede, Azure SQL Edge 'de bir T-SQL akış işi oluşturma işlemi açıklanmaktadır. Dış akış giriş ve çıkış nesnelerini oluşturup akış işi oluşturma işlemini akış işi oluşturma işleminin bir parçası olarak tanımlarsınız.

## <a name="configure-the-external-stream-input-and-output-objects"></a>Dış akış giriş ve çıkış nesnelerini yapılandırma

T-SQL akışı, akış işinin dış akış girişleri ve çıkışları ile ilişkili veri kaynaklarını tanımlamak için SQL Server dış veri kaynağı işlevselliğini kullanır. Dış akış girişi veya çıkış nesnesi oluşturmak için aşağıdaki T-SQL komutlarını kullanın:

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [Dış AKıŞ oluşturma (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

Ayrıca, Azure SQL Edge, SQL Server veya Azure SQL veritabanı bir çıkış akışı olarak kullanılıyorsa, [VERITABANı KAPSAMLı KIMLIK bilgileri (Transact-SQL) oluşturmanız](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)gerekir. Bu T-SQL komutu, veritabanına erişmek için kimlik bilgilerini tanımlar.

### <a name="supported-input-and-output-stream-data-sources"></a>Desteklenen giriş ve çıkış akışı veri kaynakları

Azure SQL Edge Şu anda yalnızca akış girişleri ve çıkışları olarak aşağıdaki veri kaynaklarını destekler.

| Veri kaynağı türü | Girdi | Çıktı | Description |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub 'ı | E | E | Bir Azure IoT Edge hub 'ına akış verilerini okumak ve yazmak için veri kaynağı. Daha fazla bilgi için bkz. [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub).|
| SQL Veritabanı | N | E | SQL veritabanına akış verileri yazmak için veri kaynağı bağlantısı. Veritabanı, Azure SQL Edge 'deki bir yerel veritabanı veya SQL Server ya da Azure SQL veritabanı 'ndaki uzak bir veritabanı olabilir.|
| Kafka | E | N | Bir Kafka konusunun akış verilerini okumak için veri kaynağı. Bu bağdaştırıcı Şu anda yalnızca Azure SQL Edge 'in Intel veya AMD sürümlerinde kullanılabilir. Azure SQL Edge 'in ARM64 sürümünde kullanılamaz.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Örnek: Azure IoT Edge hub 'ı için dış akış giriş/çıkış nesnesi oluşturma

Aşağıdaki örnek Azure IoT Edge Hub için bir dış akış nesnesi oluşturur. Azure IoT Edge Hub için bir dış akış giriş/çıkış veri kaynağı oluşturmak için, önce okunan veya yazılan verilerin düzeni için bir dış dosya biçimi oluşturmanız gerekir.

1. JSON türünde bir dış dosya biçimi oluşturun.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Azure IoT Edge hub 'ı için bir dış veri kaynağı oluşturun. Aşağıdaki T-SQL betiği, Azure SQL Edge ile aynı Docker ana bilgisayarında çalışan bir IoT Edge hub 'ına bir veri kaynağı bağlantısı oluşturur.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Azure IoT Edge Hub için dış akış nesnesi oluşturun. Aşağıdaki T-SQL betiği IoT Edge hub 'ı için bir Stream nesnesi oluşturur. Bir IoT Edge hub akış nesnesi söz konusu olduğunda, konum parametresi, okunan veya yazılan kanalın IoT Edge hub konusunun adıdır.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>Örnek: Azure SQL veritabanı 'na dış akış nesnesi oluşturma

Aşağıdaki örnek, Azure SQL Edge 'de yerel veritabanında bir dış akış nesnesi oluşturur. 

1. Veritabanında bir ana anahtar oluşturun. Kimlik bilgisi gizliliğini şifrelemek için bu gereklidir.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. SQL Server kaynağına erişmek için veritabanı kapsamlı kimlik bilgileri oluşturun. Aşağıdaki örnek, KIMLIK = username ve SECRET = Password ile dış veri kaynağına ilişkin bir kimlik bilgisi oluşturur.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Dış VERI kaynağı oluştur ile bir dış veri kaynağı oluşturun. Aşağıdaki örnek:

    * *LocalSQLOutput* adlı bir dış veri kaynağı oluşturur.
    * Dış veri kaynağını tanımlar (konum = ' <vendor> :// <server> [: <port> ] '). Örnekte, Azure SQL Edge 'in yerel bir örneğine işaret eder.
    * Daha önce oluşturulan kimlik bilgisini kullanır.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. Dış akış nesnesini oluşturun. Aşağıdaki örnek, dbo tablosuna işaret eden bir dış akış nesnesi oluşturur *. TemperatureMeasurements*, *mysqldatabase* veritabanında.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>Örnek: Kafka için dış akış nesnesi oluşturma

Aşağıdaki örnek, Azure SQL Edge 'de yerel veritabanında bir dış akış nesnesi oluşturur. Bu örnekte, Kafka sunucusunun anonim erişim için yapılandırıldığı varsayılır. 

1. Dış VERI kaynağı oluştur ile bir dış veri kaynağı oluşturun. Aşağıdaki örnek:

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Kafka girişi için bir dış dosya biçimi oluşturun. Aşağıdaki örnek, Gdaraltılmış sıkıştırmaya sahip bir JSON dosya biçimi oluşturdu. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. Dış akış nesnesini oluşturun. Aşağıdaki örnek, Kafka konusuna işaret eden bir dış akış nesnesi oluşturur `*TemperatureMeasurement*` .

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Akış işini ve akış sorgularını oluşturma

`sys.sp_create_streaming_job`Akış sorgularını tanımlamak ve akış işini oluşturmak için sistem saklı yordamını kullanın. `sp_create_streaming_job`Saklı yordam aşağıdaki parametreleri alır:

- `job_name`: Akış işinin adı. Akış işi adları, örnek genelinde benzersizdir.
- `statement`: [Stream Analytics sorgu Dil](/stream-analytics-query/stream-analytics-query-language-reference)tabanlı akış sorgu deyimleri.

Aşağıdaki örnek, bir akış sorgusuyla basit bir akış işi oluşturur. Bu sorgu IoT Edge hub 'ından girdileri okur ve `dbo.TemperatureMeasurements` veritabanına yazar.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

Aşağıdaki örnek birden çok farklı sorguya sahip daha karmaşık bir akış işi oluşturur. Bu sorgular `AnomalyDetection_ChangePoint` , sıcaklık verilerinde bozukluklar belirlemek için yerleşik işlevi kullanan bir tane içerir.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Akış işlerini başlatma, durdurma, bırakma ve izleme

Azure SQL Edge 'de bir akış işi başlatmak için `sys.sp_start_streaming_job` saklı yordamı çalıştırın. Saklı yordam, giriş olarak, başlangıç için akış işinin adını gerektirir.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Bir akış işini durdurmak için `sys.sp_stop_streaming_job` saklı yordamı çalıştırın. Saklı yordam, giriş olarak, durdurulacak akış işinin adını gerektirir.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Bir akış işini bırakmak (veya silmek) için `sys.sp_drop_streaming_job` saklı yordamı çalıştırın. Saklı yordam, giriş olarak, akış işinin adını gerektirir.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Bir akış işinin geçerli durumunu almak için `sys.sp_get_streaming_job` saklı yordamı çalıştırın. Saklı yordam, giriş olarak, akış işinin adını gerektirir. Akış işinin adını ve geçerli durumunu çıktı olarak verir.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

Akış işi aşağıdaki durumlardan herhangi birine sahip olabilir:

| Durum | Açıklama |
|--------| ------------|
| Oluşturulan | Akış işi oluşturuldu, ancak henüz başlatılmadı. |
| Başlatılıyor | Akış işi başlangıç aşamasındadır. |
| Boş | Akış işi çalışıyor, ancak işlenecek giriş yok. |
| İşleniyor | Akış işi çalışıyor ve girişleri işliyor. Bu durum, akış işi için sağlıklı bir durumu gösterir. |
| Düzeyi düşürüldü | Akış işi çalışıyor, ancak giriş işlenirken önemli olmayan bazı hatalar oluştu. Giriş işi çalışmaya devam eder, ancak hatalarla karşılaşan girdileri de bırakacak. |
| Durduruldu | Akış işi durduruldu. |
| Başarısız | Akış işi başarısız oldu. Bu genellikle işlem sırasında önemli bir hatanın göstergesidir. |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL Edge 'de akış işleriyle ilişkili meta verileri görüntüleme](streaming-catalog-views.md) 
- [Dış akış oluşturma](create-external-stream-transact-sql.md)