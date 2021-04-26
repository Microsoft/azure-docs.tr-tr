---
title: Azure Databricks Delta Gölü içine ve veri kopyalama
description: Bir Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak, Azure Databricks Delta Gölü içine ve veri kopyalama hakkında bilgi edinin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/29/2021
ms.openlocfilehash: fcf533ad95e2567e62d44d6997752df6f3145ecb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726796"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Azure Databricks Delta Gölü 'a ve Azure Data Factory kullanarak veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure Databricks Delta Gölü veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [Azure Data Factory makalesinde kopyalama etkinliği](copy-activity-overview.md) oluşturur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure Databricks Delta Lake Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Etkinliği](copy-activity-overview.md) [desteklenen bir kaynak/havuz matris](copy-activity-overview.md) tablosu ile kopyalama
- [Arama etkinliği](control-flow-lookup-activity.md)

Genel olarak, Azure Data Factory çeşitli ihtiyaçlarınızı karşılamak için aşağıdaki özelliklere sahip Delta Gölü 'yi destekler.

- Kopyalama etkinliği, desteklenen herhangi bir kaynak veri deposundan verileri Azure Databricks Delta Gölü tabloya ve Delta Gölü tablosundan desteklenen herhangi bir havuz veri deposuna kopyalamak için Azure Databricks Delta Lake bağlayıcısını destekler. Veri hareketini gerçekleştirmek için Databricks kümenizi kullanır, [Önkoşullar bölümündeki Ayrıntılar bölümüne](#prerequisites)bakın.
- [Veri akışı eşleme](concepts-data-flow-overview.md) , Azure depolama 'da, kod ücretsiz ETL için Delta dosyalarını okuyup yazmak üzere kaynak ve havuz olarak genel [Delta biçimini](format-delta.md) destekler ve yönetilen Azure Integration Runtime çalışır.
- [Databricks etkinlikleri](transform-data-databricks-notebook.md) , kod merkezli ETL veya makine öğrenimi iş yükünüzü Delta Gölü üzerine düzenlemenizi destekler.

## <a name="prerequisites"></a>Önkoşullar

Bu Azure Databricks Delta Lake bağlayıcısını kullanmak için, Azure Databricks bir küme ayarlamanız gerekir.

- Delta Gölü verileri kopyalamak için kopyalama etkinliği, özgün kaynağınız veya bir hazırlama Data Factory alanı olan bir Azure depolama alanındaki verileri okumak için Azure Databricks kümesini çağırır. Bu, kaynak verileri yerleşik hazırlanmış kopya aracılığıyla yazar. [Kanal olarak Delta Lake](#delta-lake-as-sink)'ten daha fazla bilgi edinin.
- Benzer şekilde, Delta Gölü verileri kopyalamak için kopyalama etkinliği, özgün havuzunuzu ya da Data Factory yerleşik olarak hazırlanan kopya aracılığıyla son havuza veri yazmaya devam ettiği bir hazırlama alanı olan bir Azure depolama birimine veri yazmak için Azure Databricks kümesini çağırır. [Kaynak olarak Delta](#delta-lake-as-source)Gölü hakkında daha fazla bilgi edinin.

Databricks kümesinin, kaynak/havuz/hazırlama için kullanılan depolama kapsayıcısı/dosya sistemi ve Delta Gölü tabloları yazmak istediğiniz kapsayıcı/dosya sistemi için Azure Blob veya Azure Data Lake Storage 2. hesabına erişimi olması gerekir.

- **Azure Data Lake Storage 2.** kullanmak Için, Databricks kümesinde Apache Spark yapılandırmasının bir parçası olarak bir **hizmet sorumlusu** yapılandırabilirsiniz. [Doğrudan hizmet sorumlusu Ile erişim](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20)içindeki adımları izleyin.

- **Azure Blob depolamayı** kullanmak için, Apache Spark yapılandırmasının bir parçası olarak Databricks kümesinde bir **depolama hesabı erişim anahtarı** veya **SAS belirteci** yapılandırabilirsiniz. [RDD API 'sini kullanarak Azure Blob depolama erişimi](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api)içindeki adımları izleyin.

Kopyalama etkinliği yürütülürken, yapılandırdığınız küme sonlandırılırsa, Data Factory otomatik olarak başlatılır. Data Factory yazma Kullanıcı arabirimini kullanarak işlem hattını yazarınızda, veri önizleme gibi işlemler için canlı bir kümeniz olması gerekir, Data Factory kümeyi sizin adınıza başlatmayacaktır.

#### <a name="specify-the-cluster-configuration"></a>Küme yapılandırmasını belirtin

1. **Küme modu** açılır penceresinde **Standart**' ı seçin.

2. **Databricks Runtime sürüm** açılır penceresinde bir Databricks çalışma zamanı sürümü seçin.

3. [Spark yapılandırmanıza](/azure/databricks/clusters/configure#spark-config)aşağıdaki özellikleri ekleyerek [Otomatik iyileştirme](/azure/databricks/delta/optimizations/auto-optimize) 'yi açın:

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Uygulamanızı tümleştirme ve ölçeklendirme gereksinimlerinize göre yapılandırın.

Küme yapılandırma ayrıntıları için bkz. [kümeleri yapılandırma](/azure/databricks/clusters/configure).

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, bir Azure Databricks Delta Gölü bağlayıcısına özgü Data Factory varlıkları tanımlayan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler Azure Databricks bir Delta Lake bağlı hizmeti için desteklenir.

| Özellik    | Açıklama                                                  | Gerekli |
| :---------- | :----------------------------------------------------------- | :------- |
| tür        | Type özelliği **AzureDatabricksDeltaLake** olarak ayarlanmalıdır. | Yes      |
| etki alanı      | Azure Databricks çalışma alanı URL 'sini belirtin, örn. `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . |          |
| Lclusterıd   | Var olan bir kümenin küme KIMLIĞINI belirtin. Önceden oluşturulmuş bir etkileşimli küme olmalıdır. <br>Databricks çalışma alanı-> kümeleri-> etkileşimli küme adı-> yapılandırma-> etiketleri üzerinde etkileşimli bir kümenin küme KIMLIĞINI bulabilirsiniz. [Daha fazla bilgi edinin](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | Azure Databricks kimlik doğrulaması için Data Factory erişim belirteci gereklidir. Erişim belirtecinin databricks çalışma alanından oluşturulması gerekir. Erişim belirtecini bulmak için daha ayrıntılı adımlar [burada](/azure/databricks/dev-tools/api/latest/authentication#generate-token)bulunabilir. |          |
| connectVia  | Veri deposuna bağlanmak için kullanılan [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını (veri depolubir özel ağda bulunuyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanını kullanır. | No       |

**Örnek:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. 

Aşağıdaki özellikler Azure Databricks Delta Lake veri kümesi için desteklenir.

| Özellik  | Açıklama                                                  | Gerekli                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tür      | Veri kümesinin Type özelliği **AzureDatabricksDeltaLakeDataset** olarak ayarlanmalıdır. | Yes                         |
| database | Veritabanının adı. |Kaynak için Hayır, havuz için Evet  |
| tablo | Delta tablosunun adı. |Kaynak için Hayır, havuz için Evet  |

**Örnek:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm Azure Databricks Delta Gölü kaynak ve havuz tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="delta-lake-as-source"></a>Kaynak olarak Delta Gölü

Azure Databricks Delta Gölü verileri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir.

| Özellik                     | Açıklama                                                  | Gerekli |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tür                         | Kopyalama etkinliği kaynağının Type özelliği **AzureDatabricksDeltaLakeSource** olarak ayarlanmalıdır. | Yes      |
| sorgu          | Verileri okumak için SQL sorgusunu belirtin. Zaman seyahat denetimi için aşağıdaki kalıbı izleyin:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | No       |
| exportSettings | Delta tablosundan verileri almak için kullanılan gelişmiş ayarlar. | No       |
| ***Altında `exportSettings` :*** |  |  |
| tür | Export komutunun türü, **AzureDatabricksDeltaLakeExportCommand** olarak ayarlanır. | Yes |
| dateFormat | Tarih türünü Tarih biçimiyle String olarak biçimlendirin. Özel tarih biçimleri, [Tarih saat](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)düzeninde biçimleri izler. Belirtilmemişse, varsayılan değeri kullanır `yyyy-MM-dd` . | No |
| timestampFormat | Zaman damgası türü, zaman damgası biçimindeki dize olarak biçimlendirilir. Özel tarih biçimleri, [Tarih saat](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)düzeninde biçimleri izler. Belirtilmemişse, varsayılan değeri kullanır `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-from-delta-lake"></a>Delta Gölü doğrudan kopya

Havuz veri deponuzu ve formatı bu bölümde açıklanan ölçütlere uyuyorsa, Azure Databricks Delta tablosundan doğrudan havuza kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Data Factory ayarları denetler ve aşağıdaki kriterler karşılanmazsa kopyalama etkinliğinin çalıştırılmasına başarısız olur:

- **Havuz bağlantılı hizmeti** , [Azure Blob depolama](connector-azure-blob-storage.md) veya [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md). Hesap kimlik bilgileri Azure Databricks kümesi yapılandırmasında önceden yapılandırılmış olmalıdır, [önkoşullardan](#prerequisites)daha fazla bilgi edinin.

- **Havuz veri biçimi** , aşağıdaki yapılandırmalara sahip olan **Parquet**, **ayrılmış metin** veya **avro** ve dosya yerine bir klasöre işaret ediyor.

    - **Parquet** biçimi için, sıkıştırma codec 'i **none**, **Snappy** veya **gzip** olur.
    - **Sınırlandırılmış metin** biçimi için:
        - `rowDelimiter` herhangi bir tek karakterdir.
        - `compression`**none**, **bzip2**, **gzip** olabilir.
        - `encodingName` UTF-7 desteklenmez.
    - **Avro** biçimi için, sıkıştırma codec bileşeni **none**, **söndür** veya **Snappy** olur.

- Kopyalama etkinliği kaynağında `additionalColumns` belirtilmedi.
- Verileri ayrılmış metne kopyalama, kopyalama etkinliği havuzunda, `fileExtension` ". csv" olması gerekir.
- Kopyalama etkinliği eşlemesi içinde, tür dönüştürme etkin değil.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Delta Gölü hazırlanan kopya

Havuz veri depoluğiniz veya biçimi, son bölümde belirtildiği gibi doğrudan kopyalama ölçütleriyle eşleşmezse, geçici bir Azure depolama örneği kullanarak yerleşik hazırlanmış kopyayı etkinleştirin. Hazırlanan kopya özelliği de size daha iyi aktarım hızı sağlar. Data Factory, Azure Databricks Delta Gölü verileri hazırlama depolama alanına aktarır, ardından verileri havuza kopyalar ve son olarak hazırlama depolamadan geçici verilerinizi temizler. Hazırlama kullanarak veri kopyalama hakkındaki ayrıntılar için bkz. [aşamalı kopya](copy-activity-performance-features.md#staged-copy) .

Bu özelliği kullanmak için, bir [Azure Blob depolama bağlı hizmeti](connector-azure-blob-storage.md#linked-service-properties) veya depolama hesabına ara hazırlama olarak başvuran [bağlı hizmeti Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#linked-service-properties) oluşturun. Sonra `enableStaging` `stagingSettings` kopyalama etkinliğinde ve özelliklerini belirtin.

>[!NOTE]
>Hazırlama depolama hesabı kimlik bilgileri, Azure Databricks kümesi yapılandırmasında önceden yapılandırılmış olmalıdır, [önkoşullardan](#prerequisites)daha fazla bilgi edinin.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Havuz olarak Delta Gölü

Azure Databricks Delta Gölü 'a veri kopyalamak için, etkinlik **havuzunu** kopyalama bölümünde aşağıdaki özellikler desteklenir.

| Özellik      | Açıklama                                                  | Gerekli |
| :------------ | :----------------------------------------------------------- | :------- |
| tür          | Kopyalama etkinliği havuzunun Type özelliği, **AzureDatabricksDeltaLakeSink** olarak ayarlanır. | Yes      |
| Ön Copyscrıpt | Her çalıştırmada Databricks Delta tablosuna veri yazmadan önce çalıştırılacak kopyalama etkinliğinin bir SQL sorgusunu belirtin. Bu özelliği, önceden yüklenmiş verileri temizlemek veya TRUNCATE TABLE ya da vakum deyimleri eklemek için kullanabilirsiniz. | No       |
| importSettings | Delta tablosuna veri yazmak için kullanılan gelişmiş ayarlar. | No |
| ***Altında `importSettings` :*** |                                                              |  |
| tür | Import komutunun türü, **AzureDatabricksDeltaLakeImportCommand** olarak ayarlanır. | Yes |
| dateFormat | Dize türünü Tarih biçimiyle biçimlendirin. Özel tarih biçimleri, [Tarih saat](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)düzeninde biçimleri izler. Belirtilmemişse, varsayılan değeri kullanır `yyyy-MM-dd` . | No |
| timestampFormat | Dizeyi bir zaman damgası biçimiyle zaman damgası türüne biçimlendirin. Özel tarih biçimleri, [Tarih saat](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)düzeninde biçimleri izler. Belirtilmemişse, varsayılan değeri kullanır `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-to-delta-lake"></a>Delta Gölü doğrudan kopyalama

Kaynak veri depoluünüz ve biçimlendirmeniz bu bölümde açıklanan ölçütlere uyuyorsa, kaynaktan Azure Databricks Delta Gölü 'a doğrudan kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Azure Data Factory ayarları denetler ve aşağıdaki kriterler karşılanmazsa kopyalama etkinliğinin çalıştırılmasına başarısız olur:

- **Kaynak bağlı hizmet** [Azure Blob depolama](connector-azure-blob-storage.md) veya [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md). Hesap kimlik bilgileri Azure Databricks kümesi yapılandırmasında önceden yapılandırılmış olmalıdır, [önkoşullardan](#prerequisites)daha fazla bilgi edinin.

- **Kaynak veri biçimi** , aşağıdaki yapılandırmalara sahip olan **Parquet**, **ayrılmış metin** veya **avro** ve dosya yerine bir klasöre işaret ediyor.

    - **Parquet** biçimi için, sıkıştırma codec 'i **none**, **Snappy** veya **gzip** olur.
    - **Sınırlandırılmış metin** biçimi için:
        - `rowDelimiter` Varsayılan olarak veya bir tek karakter.
        - `compression`**none**, **bzip2**, **gzip** olabilir.
        - `encodingName` UTF-7 desteklenmez.
    - **Avro** biçimi için, sıkıştırma codec bileşeni **none**, **söndür** veya **Snappy** olur.

- Kopyalama etkinliği kaynağı: 

    - `wildcardFileName` Yalnızca joker karakter içeriyor `*` `?` , ancak `wildcardFolderName` belirtilmemiş.
    - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` , ve `enablePartitionDiscovery` belirtilmedi.
    - `additionalColumns` belirtilmedi.

- Kopyalama etkinliği eşlemesi içinde, tür dönüştürme etkin değil.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Delta Gölü için hazırlanan kopya

Kaynak veri depoluğiniz veya biçimi, son bölümde belirtildiği gibi doğrudan kopyalama ölçütleriyle eşleşmezse, geçici bir Azure depolama örneği kullanarak yerleşik hazırlanmış kopyayı etkinleştirin. Hazırlanan kopya özelliği de size daha iyi aktarım hızı sağlar. Data Factory verileri otomatik olarak veri biçimi gereksinimlerine uyacak şekilde, hazırlama depolama alanına dönüştürür ve sonra verileri Delta Gölü içine yükler. Son olarak, depolama alanından geçici verilerinizi temizler. Hazırlama kullanarak veri kopyalama hakkındaki ayrıntılar için bkz. [aşamalı kopya](copy-activity-performance-features.md#staged-copy) .

Bu özelliği kullanmak için, bir [Azure Blob depolama bağlı hizmeti](connector-azure-blob-storage.md#linked-service-properties) veya depolama hesabına ara hazırlama olarak başvuran [bağlı hizmeti Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#linked-service-properties) oluşturun. Sonra `enableStaging` `stagingSettings` kopyalama etkinliğinde ve özelliklerini belirtin.

>[!NOTE]
>Hazırlama depolama hesabı kimlik bilgileri, Azure Databricks kümesi yapılandırmasında önceden yapılandırılmış olmalıdır, [önkoşullardan](#prerequisites)daha fazla bilgi edinin.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>İzleme

Azure Data Factory, diğer bağlayıcılar olarak aynı [kopyalama etkinliği izleme deneyimini](copy-activity-monitoring.md) sağlar. Ayrıca, Delta Gölü 'dan/sürümünden veri yükleme Azure Databricks kümenizde çalışırken, [ayrıntılı küme günlüklerini](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) daha da görüntüleyebilir ve [performansı izleyebilirsiniz](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özellikler hakkında daha fazla bilgi için bkz. [arama etkinliği](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Sonraki adımlar

Data Factory ' de kopyalama etkinliğine göre kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).
