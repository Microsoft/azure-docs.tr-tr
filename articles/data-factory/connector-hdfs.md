---
title: Azure Data Factory kullanarak, verileri bir sunucudan kopyalayın
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliği kullanarak bir buluttan veya şirket içi bir sunucudan, desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 49285ac4a15cb52f04f6d629d80449eb515c33fa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680857"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Azure Data Factory kullanarak, verileri bir sunucudan kopyalayın
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-hdfs-connector.md)
> * [Geçerli sürüm](connector-hdfs.md)

Bu makalede, verileri, bir sunucu sunucusu ' ndan nasıl kopyalanacağı özetlenmektedir. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu bağlantı, aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Özellikle, bu, bu bağlantı, şunları destekler:

- **Windows** (Kerberos) veya **anonim** kimlik doğrulaması kullanarak dosyaları kopyalama.
- **Web** , protokol veya **yerleşik dıtcp** desteği kullanarak dosyaları kopyalama.
- Dosya kopyalama veya [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md)ile dosyaları ayrıştırma/oluşturma/oluşturma.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Integration Runtime, Hadoop kümesinin [name node Server]: [name node port] ve [Data node Servers]: [veri düğümü bağlantı noktası **] öğesine** erişebildiğinizden emin olun. Varsayılan [ad düğümü bağlantı noktası] 50070, varsayılan [veri düğümü bağlantı noktası] 50075 ' dir.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Data Factory 'e özgü varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Şu özellikler, "

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type **özelliği:,** olarak ayarlanmalıdır. | Evet |
| url |Bu URL 'nin URL 'si |Evet |
| authenticationType | İzin verilen değerler: **Anonymous**veya **Windows**. <br><br> Bir bağlantı **kimliği Için Kerberos kimlik doğrulaması** kullanmak üzere, şirket içi ortamınızı uygun şekilde ayarlamak için [Bu bölüme](#use-kerberos-authentication-for-hdfs-connector) bakın. |Evet |
| Nitelen |Windows kimlik doğrulaması için Kullanıcı adı. Kerberos kimlik doğrulaması için `<username>@<domain>.com`belirtin. |Evet (Windows kimlik doğrulaması için) |
| password |Windows kimlik doğrulaması için parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Evet (Windows kimlik doğrulaması için) |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

**Örnek: anonim kimlik doğrulaması kullanma**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Windows kimlik doğrulamasını kullanma**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Biçim tabanlı veri kümesindeki `location` ayarları altında, aşağıdaki özellikler için desteklenir:

| Özellik   | Açıklama                                                  | Gerekli |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Veri kümesindeki `location` öğesinin altında bulunan tür özelliği **Hdfslocation**olarak ayarlanmalıdır. | Evet      |
| folderPath | Klasörün yolu. Klasörü filtrelemek için joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirtin. | Hayır       |
| fileName   | Verilen folderPath altındaki dosya adı. Dosyaları filtrelemek için joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirtin. | Hayır       |

**Örnek:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
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
| type | DataSet 'in Type özelliği: **FileShare** olarak ayarlanmalıdır |Evet |
| folderPath | Klasörün yolu. Joker karakter filtresi desteklenir, izin verilen joker karakterler şunlardır: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); gerçek dosya adınızın joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın. <br/><br/>Örnekler: RootFolder/alt klasör/, bkz. [klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek. |Evet |
| fileName |  Belirtilen "folderPath" altındaki dosya (ler) için **ad veya joker karakter filtresi** . Bu özellik için bir değer belirtmezseniz, veri kümesi klasördeki tüm dosyaları işaret eder. <br/><br/>Filtre için, izin verilen joker karakterler şunlardır: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir).<br/>-Örnek 1: `"fileName": "*.csv"`<br/>-Örnek 2: `"fileName": "???20180427.txt"`<br/>Gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın. |Hayır |
| modifiedDatetimeStart | Öznitelikleri temel alan dosya filtresi: son değiştirme. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Çok büyük miktarlarda dosyadan dosya filtresi yapmak istediğinizde, bu ayarın etkinleştirilmesi durumunda veri hareketinin genel performansını unutmayın. <br/><br/> Veri kümesine hiçbir dosya özniteliği filtresinin uygulanamadığını gösteren Özellikler NULL olabilir.  `modifiedDatetimeStart` DateTime değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değeri ile eşit veya daha büyük olan dosyalar seçilir.  `modifiedDatetimeEnd` DateTime değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| Hayır |
| modifiedDatetimeEnd | Öznitelikleri temel alan dosya filtresi: son değiştirme. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Çok büyük miktarlarda dosyadan dosya filtresi yapmak istediğinizde, bu ayarın etkinleştirilmesi durumunda veri hareketinin genel performansını unutmayın. <br/><br/> Veri kümesine hiçbir dosya özniteliği filtresinin uygulanamadığını gösteren Özellikler NULL olabilir.  `modifiedDatetimeStart` DateTime değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değeri ile eşit veya daha büyük olan dosyalar seçilir.  `modifiedDatetimeEnd` DateTime değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| Hayır |
| formatını | Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın.<br/><br/>Dosyaları belirli bir biçimde ayrıştırmak isterseniz, aşağıdaki dosya biçimi türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](supported-file-formats-and-compression-codecs.md#text-format), [JSON biçimi](supported-file-formats-and-compression-codecs.md#json-format), [avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [orc biçimi](supported-file-formats-and-compression-codecs.md#orc-format)ve [Parquet biçim](supported-file-formats-and-compression-codecs.md#parquet-format) bölümleri. |Hayır (yalnızca ikili kopya senaryosu için) |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Daha fazla bilgi için bkz. [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**.<br/>Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. |Hayır |

>[!TIP]
>Bir klasörün altındaki tüm dosyaları kopyalamak için, yalnızca **FolderPath** ' i belirtin.<br>Belirli bir ada sahip tek bir dosyayı kopyalamak için klasör **bölümü ve dosya adı ile dosya** adı ile **FolderPath** belirtin.<br>Bir klasörün altına bir dosya alt kümesini kopyalamak için klasör bölümü ve **dosya adı** joker karakter filtresi ile **FolderPath** öğesini belirtin.

**Örnek:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm,,,

### <a name="hdfs-as-source"></a>Kaynak olarak

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Biçim tabanlı kopyalama kaynağında `storeSettings` ayarları altında, aşağıdaki özellikler:

| Özellik                 | Açıklama                                                  | Gerekli                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` altındaki tür özelliği **Hdfsreadsetting**olarak ayarlanmalıdır. | Evet                                           |
| öz                | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı bir depo olduğunda, havuzda boş bir klasör veya alt klasör kopyalanmadığını veya oluşturulamadığına unutmayın. İzin verilen değerler **true** (varsayılan) ve **false**şeklindedir. | Hayır                                            |
| Yavaya Cardfolderpath       | Kaynak klasörlerin filtreleneceği joker karakter içeren klasör yolu. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın. <br>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Hayır                                            |
| Yavaya Cardfilename         | Kaynak dosyalarını filtrelemek için, belirtilen folderPath/, Cardfolderpath altındaki joker karakterlerle dosya adı. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın.  [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | `fileName` veri kümesinde belirtilmemişse Evet |
| modifiedDatetimeStart    | Öznitelikleri temel alan dosya filtresi: son değiştirme. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacak anlamına gelir.  `modifiedDatetimeStart` DateTime değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değeri ile eşit veya daha büyük olan dosyalar seçilir.  `modifiedDatetimeEnd` DateTime değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir. | Hayır                                            |
| modifiedDatetimeEnd      | Yukarıdaki gibi.                                               | Hayır                                            |
| distcpSettings | , Bir özellik grubu. | Hayır |
| resourceManagerEndpoint | Yarn Kaynak Yöneticisi uç noktası | DistCp kullanılıyorsa Evet |
| tempScriptPath | Temp Dıtcp komut betiğini depolamak için kullanılan bir klasör yolu. Betik dosyası Data Factory tarafından oluşturulur ve kopyalama işi tamamlandıktan sonra kaldırılır. | DistCp kullanılıyorsa Evet |
| distcpOptions | DistCp komutuna ek seçenekler sağlanmaktadır. | Hayır |
| maxConcurrentConnections | Depolama deposuna aynı anda bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır                                            |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSetting",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
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

#### <a name="legacy-source-model"></a>Eski kaynak modeli

>[!NOTE]
>Aşağıdaki kopya kaynak modeli, geriye dönük uyumluluk için olduğu gibi hala desteklenmektedir. Yukarıdaki ileri giderek bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **Hdfssource** olarak ayarlanmalıdır |Evet |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı depo ise, boş klasör/alt klasör, havuzda kopyalanmayacak/oluşturulmaz.<br/>İzin verilen değerler: **true** (varsayılan), **false** | Hayır |
| distcpSettings | , Bir özellik grubu. | Hayır |
| resourceManagerEndpoint | Yarn Kaynak Yöneticisi uç noktası | DistCp kullanılıyorsa Evet |
| tempScriptPath | Temp Dıtcp komut betiğini depolamak için kullanılan bir klasör yolu. Betik dosyası Data Factory tarafından oluşturulur ve kopyalama işi tamamlandıktan sonra kaldırılır. | DistCp kullanılıyorsa Evet |
| distcpOptions | DistCp komutuna ek seçenekler sağlanmaktadır. | Hayır |
| maxConcurrentConnections | Depolama deposuna aynı anda bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır |

**Örnek: DistCp kullanarak kopyalama etkinliğinde bir kaynak**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Bir sonraki bölümden, verileri, her yerden etkin bir şekilde kopyalamak için DistCp kullanma hakkında daha fazla bilgi edinin.

### <a name="folder-and-file-filter-examples"></a>Klasör ve dosya filtresi örnekleri

Bu bölümde, klasör yolu ve dosya adının joker karakter filtreleriyle elde edilen davranışı açıklanmaktadır.

| folderPath | fileName             | öz | Kaynak klasör yapısı ve filtre sonucu ( **kalın** olan dosyalar alınır) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (boş, Varsayılanı kullan) | false     | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**dosya2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | (boş, Varsayılanı kullan) | doğru      | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**dosya2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | false     | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*`  | `*.csv`              | doğru      | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Sunucudan verileri kopyalamak için DistCp kullanma

[Distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) , Hadoop kümesinde dağıtılmış kopya yapmak Için bir Hadoop yerel komut satırı aracıdır. Distcp komutunu çalıştırdığınızda, ilk olarak kopyalanacak tüm dosyaları listeler, Hadoop kümesinde birkaç eşleme işi oluşturur ve her bir eşleme işi kaynaktan ikili kopyayı havuza dönüştürür.

Dosyaları Azure Blob ( [hazırlanan kopya](copy-activity-performance.md)dahil) veya Azure Data Lake Store olarak kopyalamak Için distcp kullanarak etkinlik desteğini kopyalayın; Bu durumda, kendinden konak Integration Runtime çalıştırmak yerine kümenizin gücünden tamamen yararlanabilir. Özellikle kümeniz çok güçlü olduğunda daha iyi kopyalama performansı sağlar. Azure Data Factory yapılandırmanıza bağlı olarak, kopyalama etkinliği otomatik olarak bir distcp komutu oluşturun, Hadoop kümenize gönderebilirsiniz ve kopyalama durumunu izleyin.

### <a name="prerequisites"></a>Ön koşullar

Dosya kopyalamak için DistCp 'yi kullanarak (hazırlanan kopya dahil) veya Azure Data Lake Store, Hadoop kümenizin aşağıdaki gereksinimleri karşıladığından emin olun:

1. MapReduce ve Yarn Hizmetleri etkinleştirilmiştir.
2. Yarn sürümü 2,5 veya üzeri.
3. Bu sunucu hedef veri deponuzda (Azure Blob veya Azure Data Lake Store) tümleşiktir:

    - Azure Blob FileSystem, Hadoop 2,7 ' den bu yana yerel olarak desteklenir. Yalnızca Hadoop env config içinde jar yolunu belirtmeniz gerekir.
    - Azure Data Lake Store dosya sistemi, Hadoop 3.0.0-Alpha1 'den başlayarak paketlenir. Hadoop kümeniz bu sürümden düşükse, ADLS ile ilgili jar paketlerini (Azure-datalake-Store. jar) [buradan](https://hadoop.apache.org/releases.html)kümeye el ile aktarmanız ve Hadoop env config içinde jar yolunu belirtmeniz gerekir.

4. Bir Temp klasörünü, "bir" de hazırlayın. Bu Temp klasörü, DistCp kabuğu betiğini depolamak için kullanılır, bu nedenle KB düzeyinde alan kaplar.
5. Yarn 'da belirtilen kullanıcı hesabının, Yarn 'de uygulama gönderme izni olduğundan emin olun. b) alt klasör oluşturma, yukarıdaki Temp klasörü altında dosya okuma/yazma izinlerine sahiptir.

### <a name="configurations"></a>Yapılandırmalar

Bkz. TCP ile ilgili yapılandırma ve örneklere [kaynak](#hdfs-as-source) bölümünde.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Bağlantı kimliği için Kerberos kimlik doğrulaması kullan

Bir şirket içi ortamı ayarlamak için, bu şekilde iki seçenek bulunur. Büyük/küçük harf durumuna göre daha iyi bir seçim yapabilirsiniz.
* Seçenek 1: [Kerberos bölgesindeki şirket içinde barındırılan Integration Runtime makineye ekleme](#kerberos-join-realm)
* 2\. seçenek: [Windows etki alanı ve Kerberos bölgesi arasında karşılıklı güveni etkinleştirin](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Seçenek 1: Kerberos bölgesindeki şirket içinde barındırılan Integration Runtime makineye ekleme

#### <a name="requirements"></a>Gereksinimler

* Şirket içinde barındırılan Integration Runtime makinenin Kerberos bölgesine katılması ve herhangi bir Windows etki alanına katılamıyor olması gerekir.

#### <a name="how-to-configure"></a>Yapılandırma

**Şirket içinde barındırılan Integration Runtime makinesinde:**

1.  Kerberos KDC sunucusunu ve bölgesini yapılandırmak için **Ksetup** yardımcı programını çalıştırın.

    Kerberos bölgesi bir Windows etki alanından farklı olduğundan, makinenin bir çalışma grubunun üyesi olarak yapılandırılması gerekir. Bu, Kerberos bölgesi ayarlanarak ve bir KDC sunucusunu aşağıdaki gibi ekleyerek elde edilebilir. *Realm.com* değerini, gereken şekilde kendi ilgili bölge ile değiştirin.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    Bu 2 komutu yürüttükten sonra makineyi **yeniden başlatın** .

2.  **Ksetup** komutuyla yapılandırmayı doğrulayın. Çıktının şöyle olması gerekir:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Azure Data Factory:**

* Bir **Windows kimlik doğrulamasını** kullanarak, bu TBU veri kaynağına bağlanmak Için, Kerberos asıl adınız ve parolanızla birlikte, Yapılandırma ayrıntıları sayfasında, bu [bağlantı hizmeti özellikleri](#linked-service-properties) bölümüne bakın.

### <a name="kerberos-mutual-trust"></a>2. seçenek: Windows etki alanı ve Kerberos bölgesi arasında karşılıklı güveni etkinleştirin

#### <a name="requirements"></a>Gereksinimler

*   Şirket içinde barındırılan Integration Runtime makinenin bir Windows etki alanına katılması gerekir.
*   Etki alanı denetleyicisinin ayarlarını güncelleştirmek için izninizin olması gerekir.

#### <a name="how-to-configure"></a>Yapılandırma

> [!NOTE]
> Aşağıdaki öğreticide, REALM.COM ve AD.COM ' i kendi ilgili bölge ve etki alanı denetleyiciyle gerektiği şekilde değiştirin.

**KDC sunucusunda:**

1. KDC güveni Windows etki alanının aşağıdaki yapılandırma şablonuna başvurmasına izin vermek için **krb5. conf** dosyasındaki KDC yapılandırmasını düzenleyin. Varsayılan olarak, yapılandırma **/etc/kronb5,conf**konumunda bulunur.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   Yapılandırmadan sonra KDC hizmetini **yeniden başlatın** .

2. KDC sunucusunda **krbtgt/Realm. COM\@ad.com** adlı bir sorumlusu aşağıdaki komutla hazırlayın:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. **Hadoop. Security. auth_to_local** bir hizmet yapılandırma dosyasında `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`ekleyin.

**Etki alanı denetleyicisinde:**

1.  Bir bölge girişi eklemek için aşağıdaki **Ksetup** komutlarını çalıştırın:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Windows etki alanından Kerberos bölgesine güven oluşturun. [password] sorumlu **krbtgt/Realm. COM\@ad.com**parolasıdır.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Kerberos 'da kullanılan şifreleme algoritmasını seçin.

    1. Sunucu Yöneticisi > grup ilkesi Yönetim > etki alanı > Grup İlkesi nesneleri > varsayılan veya etkin etki alanı Ilkesi ' ne gidin ve düzenleyin.

    2. **Grup İlkesi Yönetimi Düzenleyicisi** açılır penceresinde, bilgisayar yapılandırması > Ilkeler > Windows ayarları > güvenlik ayarları > yerel Ilkeler > güvenlik seçenekleri ' ne gidin ve **ağ güvenliğini yapılandırın: şifreleme türlerini yapılandırın Kerberos için izin verilir**.

    3. KDC 'ye bağlanırken kullanmak istediğiniz şifreleme algoritmasını seçin. Genellikle, tüm seçenekleri seçmeniz yeterlidir.

        ![Kerberos için yapılandırma şifreleme türleri](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Belirli bir bölgede kullanılacak şifreleme algoritmasını belirtmek için **Ksetup** komutunu kullanın.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Windows etki alanında Kerberos sorumlusu kullanmak için etki alanı hesabı ve Kerberos sorumlusu arasındaki eşlemeyi oluşturun.

    1. **Kullanıcı ve bilgisayar Active Directory**yönetim araçlarını > başlatın.

    2. Gelişmiş özellikleri **görüntüle** > **Gelişmiş Özellikler**' i tıklatarak yapılandırın.

    3. Eşleme oluşturmak istediğiniz hesabı bulun ve **ad eşlemelerini** görüntülemek için sağ tıklayın > **Kerberos adları** sekmesi ' ne tıklayın.

    4. Realm 'tan bir sorumlu ekleyin.

        ![Eşleme güvenlik kimliği](media/connector-hdfs/map-security-identity.png)

**Şirket içinde barındırılan Integration Runtime makinesinde:**

* Bir bölge girişi eklemek için aşağıdaki **Ksetup** komutlarını çalıştırın.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory:**

* **Windows kimlik doğrulamasını** kullanarak, TBU veri kaynağına bağlanmak Için etki alanı hesabınızla veya Kerberos sorumlusuyla birlikte,, Yapılandırma ayrıntıları sayfasında, bu [bağlantı hizmeti özellikleri](#linked-service-properties) bölümüne bakın.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
