---
title: Azure Data Factory kullanarak Google bulut depolamadan veri kopyalama
description: Azure Data Factory kullanarak Google bulut depolamadan desteklenen havuz veri depolarına veri kopyalama hakkında bilgi edinin.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 4eedf54f3824adfb92ee22e5338325ccc5de3f75
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680911"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Azure Data Factory kullanarak Google bulut depolamadan veri kopyalama

Bu makalede, Google Cloud Storage 'dan verilerin nasıl kopyalanacağı özetlenmektedir. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Google Cloud Storage Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
- [Etkinliği sil](delete-activity.md)

Özellikle, bu Google Cloud Storage Bağlayıcısı, [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md)ile dosya kopyalamayı veya dosya ayrıştırmayı destekler.

>[!NOTE]
>Google Cloud Storage, S3 ile uyumlu birlikte çalışabilirlik sağladığından, Google Cloud Storage 'dan veri kopyalama, karşılık gelen özel S3 uç noktasıyla [Amazon S3 bağlayıcısını](connector-amazon-simple-storage-service.md) kullanır.

## <a name="prerequisites"></a>Ön koşullar

Google Cloud Storage hesabınızda aşağıdaki kurulum gereklidir:

1. Google Cloud Storage hesabınız için birlikte çalışabilirliği etkinleştirin
2. Kopyalamak istediğiniz verileri içeren varsayılan projeyi ayarlayın
3. Bir erişim anahtarı oluşturun.

![Google Cloud Storage için erişim anahtarını alma](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Gerekli izinler

Google Cloud Storage 'dan veri kopyalamak için aşağıdaki izinlerin verildiğinden emin olun:

- **Kopyalama etkinliği yürütmesi için:** : `s3:GetObject` ve nesne işlemleri için `s3:GetObjectVersion`.
- **Data Factory GUI yazma için**: `s3:ListAllMyBuckets` ve `s3:ListBucket``s3:GetBucketLocation` /for Bucket işlemler izinleri, test bağlantısı gibi işlemler için de gereklidir ve dosya yollarına gözatıp veya gezinilir. Bu izni vermek istemiyorsanız, bağlantılı hizmet oluşturma sayfasındaki test bağlantısını atlayın ve yolu doğrudan veri kümesi ayarlarında belirtin.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Aşağıdaki bölümlerde, Google Cloud Storage 'a özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgiler sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Google Cloud Storage bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği **GoogleCloudStorage**olarak ayarlanmalıdır. | Evet |
| Accesskeyıd | Gizli dizi erişim anahtarının KIMLIĞI. Erişim anahtarını ve gizli anahtarı bulmak için bkz. [Önkoşullar](#prerequisites). |Evet |
| secretAccessKey | Gizli dizi erişim anahtarı. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Evet |
| serviceUrl | Özel S3 uç noktasını **`https://storage.googleapis.com`** olarak belirtin. | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

Örnek aşağıda verilmiştir:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aşağıdaki özellikler, biçim tabanlı veri kümesindeki `location` ayarları altında Google bulut depolaması için desteklenir:

| Özellik   | Açıklama                                                  | Gerekli |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Veri kümesindeki `location` öğesinin altında bulunan tür özelliği **AmazonS3Location**olarak ayarlanmalıdır. | Evet      |
| bucketName | S3 demet adı.                                          | Evet      |
| folderPath | Verilen demet altındaki klasörün yolu. Klasörü filtrelemek için joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirtin. | Hayır       |
| fileName   | Belirtilen demet + folderPath altındaki dosya adı. Dosyaları filtrelemek için joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirtin. | Hayır       |

**Örnek:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="legacy-dataset-model"></a>Eski veri kümesi modeli

>[!NOTE]
>Aşağıdaki veri kümesi modeli, geriye dönük uyumluluk için olduğu gibi hala desteklenmektedir. Yukarıdaki bölümde bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **AmazonS3Object** olarak ayarlanmalıdır |Evet |
| bucketName | S3 demet adı. Joker karakter filtresi desteklenmiyor. |Kopyala/ara etkinliği için Evet, GetMetadata etkinliği için Hayır |
| anahtar | Belirtilen demet altındaki S3 nesne anahtarının **adı veya joker karakter filtresi** . Yalnızca "önek" özelliği belirtilmediğinde geçerlidir. <br/><br/>Joker karakter filtresi, hem klasör bölümü hem de dosya adı bölümü için desteklenir. İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir).<br/>-Örnek 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Örnek 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. Gerçek klasörünüz/dosya adınızın joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın. |Hayır |
| koy | S3 nesne anahtarı için ön ek. Anahtarları bu önek ile başlayan nesneler seçilidir. Yalnızca "Key" özelliği belirtilmediğinde geçerlidir. |Hayır |
| version | S3 sürümü etkinse S3 nesnesinin sürümü. |Hayır |
| modifiedDatetimeStart | Öznitelikleri temel alan dosya filtresi: son değiştirme. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacak anlamına gelir.  `modifiedDatetimeStart` DateTime değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değeri ile eşit veya daha büyük olan dosyalar seçilir.  `modifiedDatetimeEnd` DateTime değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| Hayır |
| modifiedDatetimeEnd | Öznitelikleri temel alan dosya filtresi: son değiştirme. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacak anlamına gelir.  `modifiedDatetimeStart` DateTime değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değeri ile eşit veya daha büyük olan dosyalar seçilir.  `modifiedDatetimeEnd` DateTime değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| Hayır |
| formatını | Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın.<br/><br/>Belirli bir biçimdeki dosyaları ayrıştırmak veya oluşturmak isterseniz, aşağıdaki dosya biçimi türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](supported-file-formats-and-compression-codecs.md#text-format), [JSON biçimi](supported-file-formats-and-compression-codecs.md#json-format), [avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [orc biçimi](supported-file-formats-and-compression-codecs.md#orc-format)ve [Parquet biçim](supported-file-formats-and-compression-codecs.md#parquet-format) bölümleri. |Hayır (yalnızca ikili kopya senaryosu için) |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Daha fazla bilgi için bkz. [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**.<br/>Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. |Hayır |

>[!TIP]
>Bir klasörün altındaki tüm dosyaları kopyalamak için, for Bucket için **bucketName** ve klasör bölümü için **önek** belirtin.<br>Belirli bir ada sahip tek bir dosyayı kopyalamak için, Aralık için **bucketName** ve klasör bölümü artı dosya adı için **anahtar** belirtin.<br>Bir klasörün altına bir dosya alt kümesini kopyalamak için, Bucket için **bucketName** ve klasör bölümü artı joker karakter filtresi için **anahtar** belirtin.

**Örnek: Using öneki**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Google Cloud Storage kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="google-cloud-storage-as-source"></a>Kaynak olarak Google bulut depolaması

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aşağıdaki özellikler, biçim tabanlı kopyalama kaynağında `storeSettings` ayarları altında Google bulut depolaması için desteklenir:

| Özellik                 | Açıklama                                                  | Gerekli                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | `storeSettings` altındaki tür özelliği **AmazonS3ReadSetting**olarak ayarlanmalıdır. | Evet                                                         |
| öz                | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı bir depo olduğunda, havuzda boş bir klasör veya alt klasör kopyalanmadığını veya oluşturulamadığına unutmayın. İzin verilen değerler **true** (varsayılan) ve **false**şeklindedir. | Hayır                                                          |
| koy                   | Kaynak nesneleri filtrelemek için veri kümesinde yapılandırılan belirtilen demet altındaki S3 nesne anahtarının ön eki. Anahtarları bu önek ile başlayan nesneler seçilidir. Yalnızca `wildcardFolderPath` ve `wildcardFileName` özellikleri belirtilmediğinde geçerlidir. |                                                             |
| Yavaya Cardfolderpath       | Kaynak klasörleri filtrelemek için veri kümesinde yapılandırılan belirtilen demet altında joker karakter olan klasör yolu. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın. <br>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Hayır                                                          |
| Yavaya Cardfilename         | Kaynak dosyalarını filtrelemek için, belirtilen demet + folderPath/, Cardfolderpath altındaki joker karakterlerle dosya adı. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın.  [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Veri kümesindeki `fileName` ve `prefix` belirtilmemişse Evet |
| modifiedDatetimeStart    | Öznitelikleri temel alan dosya filtresi: son değiştirme. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacak anlamına gelir.  `modifiedDatetimeStart` DateTime değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değeri ile eşit veya daha büyük olan dosyalar seçilir.  `modifiedDatetimeEnd` DateTime değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir. | Hayır                                                          |
| modifiedDatetimeEnd      | Yukarıdaki gibi.                                               | Hayır                                                          |
| maxConcurrentConnections | Depolama deposuna aynı anda bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır                                                          |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="legacy-source-model"></a>Eski kaynak modeli

>[!NOTE]
>Aşağıdaki kopya kaynak modeli, geriye dönük uyumluluk için olduğu gibi hala desteklenmektedir. Yukarıdaki ileri giderek bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **Filesystemsource** olarak ayarlanmalıdır |Evet |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı depo ise, boş klasör/alt klasör, havuzda kopyalanmayacak/oluşturulmaz.<br/>İzin verilen değerler: **true** (varsayılan), **false** | Hayır |
| maxConcurrentConnections | Depolama deposuna aynı anda bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Klasör ve dosya filtresi örnekleri

Bu bölümde, klasör yolu ve dosya adının joker karakter filtreleriyle elde edilen davranışı açıklanmaktadır.

| demet | anahtar | öz | Kaynak klasör yapısı ve filtre sonucu (kalın olan dosyalar alınır)|
|:--- |:--- |:--- |:--- |
| demet | `Folder*/*` | false | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**dosya2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| demet | `Folder*/*` | doğru | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**dosya2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| demet | `Folder*/*.csv` | false | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dosya2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| demet | `Folder*/*.csv` | doğru | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dosya2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin 

## <a name="delete-activity-properties"></a>Etkinlik özelliklerini Sil

Özelliklerle ilgili ayrıntıları öğrenmek için [silme etkinliği](delete-activity.md) onay

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
