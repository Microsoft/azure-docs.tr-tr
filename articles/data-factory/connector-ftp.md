---
title: Azure Data Factory kullanarak bir FTP sunucusundan veri kopyalama
description: Bir Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak FTP sunucusundan desteklenen havuz veri deposuna veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: a85f22381ff6d9d4568c2558beb229b4458d940a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583129"
---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>Azure Data Factory kullanarak FTP sunucusundan veri kopyalama

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
>
> * [Sürüm 1](v1/data-factory-ftp-connector.md)
> * [Güncel sürüm](connector-ftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, verilerin FTP sunucusundan nasıl kopyalanacağı özetlenmektedir. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu FTP Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
- [Etkinliği sil](delete-activity.md)

Özellikle, bu FTP Bağlayıcısı şunları destekler:

- **Temel** veya **anonim** kimlik doğrulaması kullanarak dosyaları kopyalama.
- Dosyaları olarak kopyalama veya [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md)ile dosyaları ayrıştırma.

FTP Bağlayıcısı, pasif modda çalışan FTP sunucusunu destekler. Etkin mod desteklenmiyor.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, FTP 'ye özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler FTP bağlantılı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **FtpServer** olarak ayarlanmalıdır. | Yes |
| konak | FTP sunucusunun adını veya IP adresini belirtin. | Yes |
| port | FTP sunucusunun dinlediği bağlantı noktasını belirtin.<br/>İzin verilen değerler: Integer, varsayılan değer **21**' dir. | No |
| enableSsl | Bir SSL/TLS kanalı üzerinden FTP kullanılıp kullanılmayacağını belirtin.<br/>İzin verilen değerler: **true** (varsayılan), **false**. | No |
| enableServerCertificateValidation | SSL/TLS kanalı üzerinden FTP kullanırken sunucu TLS/SSL sertifika doğrulamasının etkinleştirilip etkinleştirilmeyeceğini belirtin.<br/>İzin verilen değerler: **true** (varsayılan), **false**. | No |
| authenticationType | Kimlik doğrulama türünü belirtin.<br/>İzin verilen değerler şunlardır: **temel**, **anonim** | Yes |
| userName | FTP sunucusuna erişimi olan kullanıcıyı belirtin. | No |
| password | Kullanıcının parolasını belirtin (Kullanıcı adı). Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

>[!NOTE]
>FTP Bağlayıcısı, şifreleme veya açık SSL/TLS şifrelemesi olmadan FTP sunucusuna erişmeyi destekler; örtük SSL/TLS şifrelemesini desteklemez.

**Örnek 1: anonim kimlik doğrulaması kullanma**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek 2: temel kimlik doğrulaması kullanma**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
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

Aşağıdaki özellikler, `location` Biçim tabanlı veri kümesindeki ayarlar altında FTP için desteklenir:

| Özellik   | Açıklama                                                  | Gerekli |
| ---------- | ------------------------------------------------------------ | -------- |
| tür       | DataSet içinde Type özelliği, `location` **Ftpserverlocation** olarak ayarlanmalıdır. | Yes      |
| folderPath | Klasörün yolu. Klasörü filtrelemek için joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirtin. | No       |
| fileName   | Verilen folderPath altındaki dosya adı. Dosyaları filtrelemek için joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirtin. | No       |

**Örnek:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FtpServerLocation",
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

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, FTP kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="ftp-as-source"></a>Kaynak olarak FTP

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aşağıdaki özellikler, `storeSettings` Biçim tabanlı kopyalama kaynağı ayarları altında FTP için desteklenir:

| Özellik                 | Açıklama                                                  | Gerekli                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tür                     | Altındaki Type özelliği `storeSettings` **Ftpreadsettings** olarak ayarlanmalıdır. | Yes                                           |
| ***Kopyalanacak dosyaları bulun:*** |  |  |
| SEÇENEK 1: statik yol<br> | Veri kümesinde belirtilen klasör/dosya yolundan Kopyala. Tüm dosyaları bir klasörden kopyalamak istiyorsanız, ayrıca olarak öğesini belirtin `wildcardFileName` `*` . |  |
| Seçenek 2: joker karakter<br>-Yavaya Cardfolderpath | Kaynak klasörlerin filtreleneceği joker karakter içeren klasör yolu. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); `^` gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. <br>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | No                                            |
| Seçenek 2: joker karakter<br>-Yavaya Cardfilename | Kaynak dosyalarını filtrelemek için, belirtilen folderPath/, Cardfolderpath altındaki joker karakterlerle dosya adı. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); `^` gerçek dosya adınızın joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın.  [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Yes |
| Seçenek 3: dosya listesi<br>-fileListPath | Belirli bir dosya kümesinin kopyalanıp ayrılmadığını gösterir. Veri kümesinde yapılandırılan yolun göreli yolu olan, kopyalamak istediğiniz dosyaların listesini içeren bir metin dosyası üzerine gelin.<br/>Bu seçeneği kullanırken, veri kümesinde dosya adı belirtmeyin. [Dosya listesi örneklerinde](#file-list-examples)daha fazla örneğe bakın. |No |
| ***Ek ayarlar:*** |  | |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı bir depo olduğunda, havuzda boş bir klasör veya alt klasör kopyalanmadığını veya oluşturulamadığına unutmayın. <br>İzin verilen değerler **true** (varsayılan) ve **false** şeklindedir.<br>Bu özellik, yapılandırdığınızda uygulanmaz `fileListPath` . |No |
| deleteFilesAfterCompletion | Hedef depoya başarıyla taşıdıktan sonra, ikili dosyaların kaynak depodan silinip silinmeyeceğini gösterir. Dosya silme dosya başına yapılır, bu nedenle kopyalama etkinliği başarısız olduğunda, bazı dosyaların hedefe zaten kopyalanmış ve kaynaktan silindiği görürsünüz, diğerleri ise kaynak deposunda hala kalır. <br/>Bu özellik yalnızca ikili dosyalar kopyalama senaryosunda geçerlidir. Varsayılan değer: false. |No |
| useBinaryTransfer | İkili aktarım modunun kullanılıp kullanılmayacağını belirtin. Değerler ikili mod (varsayılan) için true, ASCII için false şeklindedir. |No |
| enablePartitionDiscovery | Bölümlenmiş dosyalar için, dosya yolundan bölümlerin ayrıştırıp ayrıştırmayacağını belirtin ve bunları ek kaynak sütunları olarak ekleyin.<br/>İzin verilen değerler **false** (varsayılan) ve **true** şeklindedir. | No                                            |
| Partitionrootyolu | Bölüm bulma etkin olduğunda, bölümlenmiş klasörleri veri sütunları olarak okumak için mutlak kök yolunu belirtin.<br/><br/>Belirtilmemişse, varsayılan olarak<br/>-Veri kümesinde dosya yolunu veya kaynaktaki dosya listesini kullandığınızda, bölüm kök yolu, veri kümesinde yapılandırılan yoldur.<br/>-Joker karakter klasörü filtresi kullandığınızda, bölüm kök yolu ilk joker karakterin öncesindeki alt yoldur.<br/><br/>Örneğin, veri kümesindeki yolu "root/Folder/Year = 2020/ay = 08/gün = 27" olarak yapılandırdığınız varsayılarak:<br/>-Bölüm kök yolunu "root/Folder/Year = 2020" olarak belirtirseniz, kopyalama etkinliği `month` `day` dosyaların içindeki sütunlara ek olarak, sırasıyla "08" ve "27" değeriyle birlikte iki sütun oluşturur.<br/>-Bölüm kök yolu belirtilmemişse, ek sütun oluşturulmaz. | No                                            |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

Veri formu FTP kopyalanırken, şu anda ADF önce dosya uzunluğunu almaya çalışır, sonra dosyayı birden çok parçaya böler ve paralel olarak okur. FTP sunucunuz dosya uzunluğunu almayı veya belirli bir sapmayı okumak için aramayı desteklemiyorsa, hatayla karşılaşabilirsiniz.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FtpReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Klasör ve dosya filtresi örnekleri

Bu bölümde, klasör yolu ve dosya adının joker karakter filtreleriyle elde edilen davranışı açıklanmaktadır.

| folderPath | fileName | öz | Kaynak klasör yapısı ve filtre sonucu ( **kalın** olan dosyalar alınır)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (boş, Varsayılanı kullan) | yanlış | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (boş, Varsayılanı kullan) | true | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | yanlış | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Dosya listesi örnekleri

Bu bölümde, kopyalama etkinlik kaynağında dosya listesi yolu kullanmanın ortaya çıkan davranışı açıklanmaktadır.

Aşağıdaki kaynak klasör yapısına sahip olduğunuz ve dosyaları kalın yazı tipinde kopyalamak istediğiniz varsayılarak:

| Örnek kaynak yapısı                                      | FileListToCopy.txt içerik                             | ADF yapılandırması                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| kök<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Veriyi<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Veri kümesinde:**<br>-Klasör yolu: `root/FolderA`<br><br>**Kopyalama etkinliği kaynağı:**<br>-Dosya listesi yolu: `root/Metadata/FileListToCopy.txt` <br><br>Dosya listesi yolu, aynı veri deposunda, kopyalamak istediğiniz dosyaların listesini içeren bir metin dosyasını işaret eder, veri kümesinde yapılandırılan yolun göreli yolu ile her satıra bir dosya. |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin 

## <a name="delete-activity-properties"></a>Etkinlik özelliklerini Sil

Özelliklerle ilgili ayrıntıları öğrenmek için [silme etkinliği](delete-activity.md) onay

## <a name="legacy-models"></a>Eski modeller

>[!NOTE]
>Geriye dönük uyumluluk için aşağıdaki modeller hala desteklenmektedir. Yukarıdaki bölümlerde bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

### <a name="legacy-dataset-model"></a>Eski veri kümesi modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **FileShare** olarak ayarlanmalıdır |Yes |
| folderPath | Klasörün yolu. Joker karakter filtresi desteklenir, izin verilen joker karakterler şunlardır: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); `^` gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. <br/><br/>Örnekler: RootFolder/alt klasör/, bkz. [klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek. |Yes |
| fileName | Belirtilen "folderPath" altındaki dosya (ler) için **ad veya joker karakter filtresi** . Bu özellik için bir değer belirtmezseniz, veri kümesi klasördeki tüm dosyaları işaret eder. <br/><br/>Filtre için, izin verilen joker karakterler şunlardır: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir).<br/>-Örnek 1: `"fileName": "*.csv"`<br/>-Örnek 2: `"fileName": "???20180427.txt"`<br/>`^`Gerçek dosya adınızın joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. |No |
| biçim | Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın.<br/><br/>Dosyaları belirli bir biçimde ayrıştırmak isterseniz, aşağıdaki dosya biçimi türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON biçimi](supported-file-formats-and-compression-codecs-legacy.md#json-format), [avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [orc biçimi](supported-file-formats-and-compression-codecs-legacy.md#orc-format)ve [Parquet biçim](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) bölümleri. |Hayır (yalnızca ikili kopya senaryosu için) |
| sıkıştırma | Verilerin türünü ve sıkıştırma düzeyini belirtin. Daha fazla bilgi için bkz. [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2** ve **zipsöndür**.<br/>Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. |No |
| useBinaryTransfer | İkili aktarım modunun kullanılıp kullanılmayacağını belirtin. Değerler ikili mod (varsayılan) için true, ASCII için false şeklindedir. |No |

>[!TIP]
>Bir klasörün altındaki tüm dosyaları kopyalamak için, yalnızca **FolderPath** ' i belirtin.<br>Belirli bir ada sahip tek bir dosyayı kopyalamak için klasör **bölümü ve dosya adı ile dosya** adı ile **FolderPath** belirtin.<br>Bir klasörün altına bir dosya alt kümesini kopyalamak için klasör bölümü ve **dosya adı** joker karakter filtresi ile **FolderPath** öğesini belirtin.

>[!NOTE]
>Dosya filtresi için "fileFilter" özelliğini kullanıyorsanız, "dosya adı" ' na eklenen yeni filtre özelliğini kullanmanız önerilirken, olduğu gibi hala desteklenmektedir.

**Örnek:**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
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

### <a name="legacy-copy-activity-source-model"></a>Eski kopyalama etkinliği kaynak modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Filesystemsource** olarak ayarlanmalıdır |Yes |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı depo ise, boş klasör/alt klasör, havuzda kopyalanmayacak/oluşturulmaz.<br/>İzin verilen değerler: **true** (varsayılan), **false** | No |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
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

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
