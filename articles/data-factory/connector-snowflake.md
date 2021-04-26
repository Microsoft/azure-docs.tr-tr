---
title: Kar tanesi içindeki verileri kopyalama ve dönüştürme
description: Data Factory kullanarak kar halinde verileri kopyalamayı ve dönüştürmeyi öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/16/2021
ms.openlocfilehash: c18a48f8e72c28fd39f839566b18528806e7245d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103561609"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Azure Data Factory kullanarak kar tanesi içindeki verileri kopyalama ve dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, ' de kopyalama etkinliğinin Azure Data Factory ' de nasıl kullanılacağı özetlenmektedir ve verileri kar ve geri kopyalamak için veri akışı kullanılır. Data Factory hakkında daha fazla bilgi için bkz. [giriş makalesi](introduction.md).

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu kar tanesi Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Etkinliği](copy-activity-overview.md) [desteklenen bir kaynak/havuz matris](copy-activity-overview.md) tablosu ile kopyalama
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Kopyalama etkinliği için, bu kar tanesi bağlayıcı aşağıdaki işlevleri destekler:

- En iyi performansı elde etmek için kar tanesine ait [kopyayı [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) komutuna kullanan kar grubundan verileri kopyalayın.
- En iyi performansı elde etmek için, kar \ ' ın [[tablo]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) komutuna kadar olan verileri kar düzeyine kopyalayın. Azure 'da kar. 

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, bir kar oluşturan bağlayıcıya özgü Data Factory varlıkları tanımlayan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler, bir kar tanesi bağlantılı hizmeti için desteklenir.

| Özellik         | Açıklama                                                  | Gerekli |
| :--------------- | :----------------------------------------------------------- | :------- |
| tür             | Tür özelliğinin **kar** tanesi olarak ayarlanması gerekir.              | Yes      |
| Dizisi | Kar tanesi örneğine bağlanmak için gereken bilgileri belirtir. Azure Key Vault bir parola veya tam bağlantı dizesi koyabilirsiniz. Daha ayrıntılı bilgi için tablonun altındaki örneklere ve [Azure Key Vault 'de mağaza kimlik bilgileri](store-credentials-in-key-vault.md) ' ne bakın.<br><br>Bazı tipik ayarlar:<br>- **Hesap adı:** Kar sunan hesabınızın  [tam hesap adı](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (bölge ve bulut platformunu tanımlayan ek segmentler dahil), örn. xy12345. Doğu-US-2. Azure.<br/>- **Kullanıcı adı:** Bağlantının kullanıcı oturum açma adı.<br>- **Parola:** Kullanıcının parolası.<br>- **Veritabanı:** Bağlandıktan sonra kullanılacak varsayılan veritabanı. Belirtilen rolün ayrıcalıkları olan mevcut bir veritabanı olmalıdır.<br>- **Ambar:** Bağlandıktan sonra kullanılacak sanal ambar. Belirtilen rolün ayrıcalıkları olan mevcut bir ambar olması gerekir.<br>- **Rol:** Kar tanesi oturumunda kullanılacak varsayılan erişim denetimi rolü. Belirtilen rol, belirtilen kullanıcıya zaten atanmış olan mevcut bir rol olmalıdır. Varsayılan rol GENELDIR. | Yes      |
| connectVia       | Veri deposuna bağlanmak için kullanılan [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını (veri depolubir özel ağda bulunuyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanını kullanır. | No       |

**Örnek:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault parola:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. 

Aşağıdaki özellikler, kar tanesi veri kümesi için desteklenir.

| Özellik  | Açıklama                                                  | Gerekli                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tür      | Veri kümesinin Type özelliği, **kar tablosu** olarak ayarlanmalıdır. | Yes                         |
| schema | Şemanın adı. Şema adı ADF 'de büyük/küçük harfe duyarlıdır. |Kaynak için Hayır, havuz için Evet  |
| tablo | Tablo/görünüm adı. ADF 'de tablo adının büyük/küçük harfe duyarlı olduğunu aklınızda edin. |Kaynak için Hayır, havuz için Evet  |

**Örnek:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
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

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, kar kaynağı ve havuz tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="snowflake-as-the-source"></a>Kaynak olarak kar tanesi

Kar tanesi Bağlayıcısı, en iyi performansı elde etmek için kar [yerinin [location] komutuna kopyalanmasını](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) kullanır.

Havuz veri deposu ve biçimi, kar tanesi kopyalama komutu tarafından yerel olarak destekleniyorsa, kar/ç 'den havuza doğrudan kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Ayrıntılar için bkz. [kar grubundan doğrudan kopyalama](#direct-copy-from-snowflake). Aksi takdirde, [kar tanesi ' nden yerleşik hazırlanmış kopyayı](#staged-copy-from-snowflake)kullanın.

Verileri kar 'lerden kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir.

| Özellik                     | Açıklama                                                  | Gerekli |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tür                         | Kopyalama etkinliği kaynağının Type özelliği **SnowflakeSource** olarak ayarlanmalıdır. | Yes      |
| sorgu          | Kar tanesi 'nden verileri okumak için SQL sorgusunu belirtir. Şema, tablo ve sütun adları küçük harf içeriyorsa, sorgu gibi nesne tanımlayıcısını tırnak içine alarak tırnak işareti `select * from "schema"."myTable"` .<br>Saklı yordamın yürütülmesi desteklenmiyor. | No       |
| exportSettings | Kar tanesi 'nden verileri almak için kullanılan gelişmiş ayarlar. Deyiminizi çağırdığınızda Data Factory geçirilecek olan COPY komutuna göre desteklenen olanları yapılandırabilirsiniz. | No       |
| ***Altında `exportSettings` :*** |  |  |
| tür | Dışa aktarma komutunun türü, **kar Keexportcopycommand** olarak ayarlanır. | Yes |
| additionalCopyOptions | Anahtar-değer çiftlerinin sözlüğü olarak belirtilen ek kopyalama seçenekleri. Örnekler: MAX_FILE_SIZE, ÜZERINE yaz. Daha fazla bilgi için bkz. [kar tanesi kopyalama seçenekleri](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | No |
| additionalFormatOptions | Komutları anahtar-değer çiftleri sözlüğü olarak kopyalamak için belirtilen ek dosya biçimi seçenekleri. Örnekler: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Daha fazla bilgi için bkz. [kar tanesi biçim türü seçenekleri](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-from-snowflake"></a>Kar tanesi 'nden doğrudan kopya

Havuz veri deprenizin ve formatı bu bölümde açıklanan ölçütlere uyuyorsa, kar/ç 'den havuza doğrudan kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Data Factory ayarları denetler ve aşağıdaki kriterler karşılanmazsa kopyalama etkinliğinin çalıştırılmasına başarısız olur:

- **Havuz bağlantılı hizmeti** , **paylaşılan erişim imzası** kimlik doğrulaması ile [**Azure Blob depolama**](connector-azure-blob-storage.md) . Verileri aşağıdaki desteklenen biçimde Azure Data Lake Storage 2. doğrudan kopyalamak istiyorsanız, kar \ ' [dan hazırlanan kopyayı](#staged-copy-from-snowflake)kullanmaktan kaçınmak için, ADLS 2. hesabınızda SAS kimlik doğrulamasıyla bir Azure Blob bağlı hizmeti oluşturabilirsiniz.

- **Havuz veri biçimi** , aşağıdaki yapılandırmalara **sahip Parquet**, **ayrılmış metin** veya **JSON** 'dir:

    - **Parquet** biçimi için, sıkıştırma codec 'i **none**, **Snappy** veya **LZO** olur.
    - **Sınırlandırılmış metin** biçimi için:
        - `rowDelimiter`**\r\n** veya herhangi bir tek karakter.
        - `compression`**sıkıştırma**, **gzip**, **bzip2** veya **söndür** olamaz.
        - `encodingName` Varsayılan olarak bırakılır veya **UTF-8** olarak ayarlanır.
        - `quoteChar`**çift tırnak**, **tek tırnak** veya **boş dize** (quote char yok).
    - **JSON** biçimi için doğrudan kopyalama yalnızca kaynak kar tablosu veya sorgu sonucunun yalnızca tek bir sütuna sahip olduğu ve bu sütunun veri türü **değişken**, **nesne** veya **dizi** olduğu durumu destekler.
        - `compression`**sıkıştırma**, **gzip**, **bzip2** veya **söndür** olamaz.
        - `encodingName` Varsayılan olarak bırakılır veya **UTF-8** olarak ayarlanır.
        - `filePattern` kopyalama etkinliği havuzu varsayılan olarak bırakılır veya **Setofobjects** olarak ayarlanır.
- Kopyalama etkinliği kaynağı ' nda `additionalColumns` belirtilmedi.
- Sütun eşleme belirtilmedi.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Kar tanesi üzerinden hazırlanan kopya

Havuz veri depoluğiniz veya biçimi, son bölümde belirtildiği gibi kar tanesi kopyalama komutuyla yerel olarak uyumlu değilse, geçici bir Azure Blob depolama örneği kullanarak yerleşik hazırlanmış kopyayı etkinleştirin. Hazırlanan kopya özelliği de size daha iyi aktarım hızı sağlar. Data Factory, kar grubundan hazırlama deposuna veri aktarır, ardından verileri havuza kopyalar ve son olarak hazırlama depolamadan geçici verilerinizi temizler. Hazırlama kullanarak veri kopyalama hakkındaki ayrıntılar için bkz. [aşamalı kopya](copy-activity-performance-features.md#staged-copy) .

Bu özelliği kullanmak için, Azure depolama hesabına ara hazırlama olarak başvuran bir [Azure Blob depolama bağlı hizmeti](connector-azure-blob-storage.md#linked-service-properties) oluşturun. Sonra `enableStaging` `stagingSettings` kopyalama etkinliğinde ve özelliklerini belirtin.

> [!NOTE]
> Hazırlama Azure Blob depolama bağlı hizmeti, kar tanesi kopyalama komutunun gerektirdiği şekilde paylaşılan erişim imzası kimlik doğrulaması kullanmalıdır. 

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
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

### <a name="snowflake-as-sink"></a>Kanal olarak kar tanesi

Kar tanesi Bağlayıcısı, en iyi performansı elde etmek için kar tabonun [kopyasını [tablo]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) komutuna kullanır. Azure 'da kar için veri yazmayı destekler.

Kaynak veri deposu ve biçim, kar tanesi kopyalama komutu tarafından yerel olarak destekleniyorsa, kaynaktan kar \ ' a doğrudan kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Ayrıntılar için bkz. [kar halinde doğrudan kopyalama](#direct-copy-to-snowflake). Aksi takdirde, [kar tanesi için yerleşik hazırlanmış kopyayı](#staged-copy-to-snowflake)kullanın.

Verileri kar 'a kopyalamak için, etkinlik **havuzunu** Kopyala bölümünde aşağıdaki özellikler desteklenir.

| Özellik          | Açıklama                                                  | Gerekli                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| tür              | Kopyalama etkinliği havuzunun Type özelliği, **SnowflakeSink** olarak ayarlanır. | Yes                                           |
| Ön Copyscrıpt     | Kopyalama etkinliği için, her çalıştırmada verileri kar alanına yazmadan önce çalıştırılacak bir SQL sorgusu belirtin. Önceden yüklenmiş verileri temizlemek için bu özelliği kullanın. | No                                            |
| importSettings | Kar ayarlarına veri yazmak için kullanılan gelişmiş ayarlar. Deyiminizi çağırdığınızda Data Factory geçirilecek olan COPY komutuna göre desteklenen olanları yapılandırabilirsiniz. | No |
| ***Altında `importSettings` :*** |                                                              |  |
| tür | Import komutunun türü, **kar Keımportcopycommand** olarak ayarlanır. | Yes |
| additionalCopyOptions | Anahtar-değer çiftlerinin sözlüğü olarak belirtilen ek kopyalama seçenekleri. Örnekler: ON_ERROR, zorla, LOAD_UNCERTAIN_FILES. Daha fazla bilgi için bkz. [kar tanesi kopyalama seçenekleri](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | No |
| additionalFormatOptions | Anahtar-değer çiftleri sözlüğü olarak sağlanmış olan COPY komutuna ek dosya biçimi seçenekleri verilmiştir. Örnekler: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Daha fazla bilgi için bkz. [kar tanesi biçim türü seçenekleri](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-to-snowflake"></a>Kar için doğrudan kopya

Kaynak veri deprenizin ve biçimlendirmeniz bu bölümde açıklanan ölçütlere uyuyorsa, kaynaktan kar/veya daha doğrudan kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Azure Data Factory ayarları denetler ve aşağıdaki kriterler karşılanmazsa kopyalama etkinliğinin çalıştırılmasına başarısız olur:

- **Kaynak bağlı hizmet** , **paylaşılan erişim imzası** kimlik doğrulaması ile [**Azure Blob deposıdır**](connector-azure-blob-storage.md) . Aşağıdaki desteklenen biçimdeki Azure Data Lake Storage 2. verileri doğrudan kopyalamak istiyorsanız, kar/Azure 'da  [hazırlanan kopyayı](#staged-copy-to-snowflake)kullanmaktan kaçınmak için, ADLS 2. hesabınızda SAS kimlik doğrulamasıyla bir Azure Blob bağlı hizmeti oluşturabilirsiniz.

- **Kaynak veri biçimi** , aşağıdaki yapılandırmalara sahip **Parquet**, **sınırlandırılmış metin** veya **JSON** ' dır:

    - **Parquet** biçimi için, sıkıştırma codec 'i **none** veya **Snappy**.

    - **Sınırlandırılmış metin** biçimi için:
        - `rowDelimiter`**\r\n** veya herhangi bir tek karakter. Satır sınırlayıcısı "\r\n" değilse, `firstRowAsHeader` **false** olması ve `skipLineCount` belirtilmemişse belirtilmemiş olması gerekir.
        - `compression`**sıkıştırma**, **gzip**, **bzip2** veya **söndür** olamaz.
        - `encodingName` Varsayılan olarak bırakılır veya "UTF-8", "UTF-16", "UTF-16TO", "UTF-32", "UTF-32AS", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "olarak ayarlanır. ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255".
        - `quoteChar`**çift tırnak**, **tek tırnak** veya **boş dize** (quote char yok).
    - **JSON** biçimi için doğrudan kopyalama yalnızca havuz kar tanesi tablosunun yalnızca tek bir sütuna sahip olduğu ve bu sütunun veri türü **değişken**, **nesne** veya **dizi** olduğu durumu destekler.
        - `compression`**sıkıştırma**, **gzip**, **bzip2** veya **söndür** olamaz.
        - `encodingName` Varsayılan olarak bırakılır veya **UTF-8** olarak ayarlanır.
        - Sütun eşleme belirtilmedi.

- Kopyalama etkinliği kaynağı: 

   -  `additionalColumns` belirtilmedi.
   - Kaynağınız bir klasörse, `recursive` true olarak ayarlanır.
   - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` , ve `enablePartitionDiscovery` belirtilmedi.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Kar için hazırlanan kopya

Kaynak veri depoluğiniz veya biçimi, son bölümde belirtildiği gibi kar tanesi kopyalama komutuyla yerel olarak uyumlu değilse, geçici bir Azure Blob depolama örneği kullanarak yerleşik hazırlanmış kopyayı etkinleştirin. Hazırlanan kopya özelliği de size daha iyi aktarım hızı sağlar. Data Factory, verileri otomatik olarak kar tanesi veri biçimi gereksinimlerini karşılayacak şekilde dönüştürür. Ardından, verileri kar halinde yüklemek için COPY komutunu çağırır. Son olarak, blob depolamadan geçici verilerinizi temizler. Hazırlama kullanarak veri kopyalama hakkındaki ayrıntılar için bkz. [aşamalı kopya](copy-activity-performance-features.md#staged-copy) .

Bu özelliği kullanmak için, Azure depolama hesabına ara hazırlama olarak başvuran bir [Azure Blob depolama bağlı hizmeti](connector-azure-blob-storage.md#linked-service-properties) oluşturun. Sonra `enableStaging` `stagingSettings` kopyalama etkinliğinde ve özelliklerini belirtin.

> [!NOTE]
> Hazırlama Azure Blob depolama bağlı hizmetinin, kar tanesi kopyalama komutunun gerektirdiği şekilde paylaşılan erişim imzası kimlik doğrulamasını kullanması gerekir.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
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

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki verileri dönüştürürken, kar/çıkış içindeki tabloları okuyabilir ve yazabilirsiniz. Daha fazla bilgi için bkz. veri akışlarını eşleme içindeki [kaynak dönüştürme](data-flow-source.md) ve [Havuz dönüşümü](data-flow-sink.md) . Kaynak ve havuz türü olarak bir kar tanesi veri kümesi veya [satır içi veri kümesi](data-flow-source.md#inline-datasets) kullanmayı tercih edebilirsiniz.

### <a name="source-transformation"></a>Kaynak dönüştürme

Aşağıdaki tabloda, kar tanesi kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz. Bağlayıcı, kar tanesi [iç veri aktarımını](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)kullanır.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tablo | Giriş olarak tablo ' yı seçerseniz, veri akışı, satır içi veri kümesi kullanılırken kar tanesi veri kümesinde veya kaynak seçeneklerinde belirtilen tablodaki tüm verileri getirir. | Hayır | Dize | *(yalnızca satır içi veri kümesi için)*<br>tableName<br>schemaName |
| Sorgu | Giriş olarak Sorgula ' yı seçerseniz, kar grubundan veri getirmek için bir sorgu girin. Bu ayar, veri kümesinde seçtiğiniz tüm tabloları geçersiz kılar.<br>Şema, tablo ve sütun adları küçük harf içeriyorsa, sorgu gibi nesne tanımlayıcısını tırnak içine alarak tırnak işareti `select * from "schema"."myTable"` . | Hayır | Dize | sorgu |

#### <a name="snowflake-source-script-examples"></a>Kar tanesi kaynak betiği örnekleri

Kaynak türü olarak kar tanesi veri kümesini kullandığınızda, ilişkili veri akışı betiği şu şekilde olur:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

Satır içi veri kümesi kullanıyorsanız, ilişkili veri akışı betiği şu şekilde olur:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>Havuz dönüştürme

Aşağıdaki tabloda, kar havuz tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **Ayarlar** sekmesinde düzenleyebilirsiniz. Satır içi veri kümesi kullanırken, [veri kümesi özellikleri](#dataset-properties) bölümünde açıklanan özelliklerle aynı olan ek ayarları görürsünüz. Bağlayıcı, kar tanesi [iç veri aktarımını](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)kullanır.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Yöntemi Güncelleştir | Kar hedefi hedefinde hangi işlemlere izin verileceğini belirtin.<br>Satırları güncelleştirmek, kaldırmak veya silmek için, bu eylemler için satırları etiketlemek üzere bir [alter Row dönüşümü](data-flow-alter-row.md) gereklidir. | Yes | `true` veya `false` | siler <br/>eklenebilir <br/>güncellenebilir <br/>upsertable |
| Anahtar sütunlar | Güncelleştirmeler, yukarı ve silme için bir anahtar sütunu veya sütunları ayarlanacak satırı belirleyecek şekilde ayarlanmalıdır. | No | Dizi | keys |
| Tablo eylemi | Yazmadan önce hedef tablodaki tüm satırların yeniden oluşturulup kaldırılacağını belirler.<br>- **Hiçbiri**: tabloya hiçbir eylem yapılmaz.<br>- **Yeniden oluştur**: tablo bırakılır ve yeniden oluşturulur. Dinamik olarak yeni bir tablo oluşturuluyoruz gereklidir.<br>- **Kes**: hedef tablodaki tüm satırlar kaldırılacak. | No | `true` veya `false` | Oluştur<br/>kesilemedi |

#### <a name="snowflake-sink-script-examples"></a>Kar tanesi havuz betiği örnekleri

Kanal türü olarak kar tanesi veri kümesini kullandığınızda, ilişkili veri akışı betiği şu şekilde olur:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

Satır içi veri kümesi kullanıyorsanız, ilişkili veri akışı betiği şu şekilde olur:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özellikler hakkında daha fazla bilgi için bkz. [arama etkinliği](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Sonraki adımlar

Data Factory ' de kopyalama etkinliğine göre kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).
