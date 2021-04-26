---
title: Amazon Simple Storage Service (S3) öğesinden veri kopyalama
description: Azure Data Factory kullanarak Amazon Simple Storage Service (S3) kaynağından desteklenen havuz veri depolarına veri kopyalama hakkında bilgi edinin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 03b0cd852f34e115cc5bbc60448e45fcbb680474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601236"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Amazon Simple Storage Service 'ten veri kopyalama
> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory hizmeti sürümünü seçin:"]
>
> * [Sürüm 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Güncel sürüm](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Amazon Simple Storage Service 'ten (Amazon S3) verilerin nasıl kopyalanacağı özetlenmektedir. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

>[!TIP]
>Amazon S3 'den Azure depolama 'ya veri geçişi senaryosu hakkında daha fazla bilgi edinmek için bkz. [Azure Data Factory kullanarak Amazon S3 'Ten Azure depolama 'ya veri geçirme](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Amazon S3 Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
- [Etkinliği sil](delete-activity.md)

Özellikle, bu Amazon S3 Bağlayıcısı, [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md)ile dosya kopyalamayı veya dosya ayrıştırmayı destekler. Ayrıca, [kopyalama sırasında dosya meta verilerini korumayı](#preserve-metadata-during-copy)seçebilirsiniz. Bağlayıcı, S3 'e yönelik isteklerin kimliğini doğrulamak için [AWS Imza sürüm 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) ' ü kullanır.

>[!TIP]
>Bu Amazon S3 bağlayıcısını kullanarak, [Google bulut depolaması](connector-google-cloud-storage.md)gibi *S3 ile uyumlu herhangi bir depolama sağlayıcısından* veri kopyalayabilirsiniz. Bağlı hizmet yapılandırmasında karşılık gelen hizmet URL 'sini belirtin.

## <a name="required-permissions"></a>Gerekli izinler

Amazon S3 'den verileri kopyalamak için, Amazon S3 nesne işlemleri için aşağıdaki izinlerin verildiğinden emin olun: `s3:GetObject` ve `s3:GetObjectVersion` .

Yazmak için Data Factory Kullanıcı arabirimini kullanıyorsanız, `s3:ListAllMyBuckets` `s3:ListBucket` / `s3:GetBucketLocation` bağlantılı hizmetle bağlantı ve köke göz atma gibi işlemler için ek ve izinler gereklidir. Bu izinleri vermek istemiyorsanız, kullanıcı arabiriminden "dosya yoluna Bağlantıyı Sına" veya "belirtilen yoldan gözatın" seçeneklerinden birini belirleyebilirsiniz.

Amazon S3 izinlerinin tam listesi için, AWS sitesindeki [bir Ilkede Izinleri belirtme](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) konusuna bakın.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Aşağıdaki bölümlerde,, Amazon S3 'e özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Amazon S3 bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | **Type** özelliği **AmazonS3** olarak ayarlanmalıdır. | Yes |
| authenticationType | Amazon S3 ' e bağlanmak için kullanılan kimlik doğrulama türünü belirtin. AWS kimlik ve erişim yönetimi (ıAM) hesabı veya [geçici güvenlik kimlik bilgileri](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)için erişim anahtarları kullanmayı seçebilirsiniz.<br>İzin verilen değerler: `AccessKey` (varsayılan) ve `TemporarySecurityCredentials` . |No |
| Accesskeyıd | Gizli dizi erişim anahtarının KIMLIĞI. |Yes |
| secretAccessKey | Gizli dizi erişim anahtarı. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Yes |
| sessionToken | [Geçici güvenlik kimlik bilgileri](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) kimlik doğrulaması kullanılırken geçerlidir. AWS 'den [geçici güvenlik kimlik bilgileri isteme](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) hakkında bilgi edinin.<br>Bkz. AWS geçici kimlik bilgileri, ayarlara göre 15 dakika ila 36 saat arasında dolar. Etkinlik yürütüldüğünde kimlik bilgilerinizin geçerli olduğundan emin olun, özellikle de çalışan iş yükü için, örneğin, uygulamayı düzenli olarak yenileyebilir ve Azure Key Vault kaydedebilirsiniz.<br>Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |No |
| serviceUrl | Resmi Amazon S3 hizmeti dışında S3 ile uyumlu bir depolama sağlayıcısından veri kopyalıyorsanız, özel S3 uç noktasını belirtin. Örneğin, Google bulut depolamadan veri kopyalamak için, belirtin `https://storage.googleapis.com` . | No |
| forcePathStyle | Sanal barındırılan stil erişimi yerine S3 [yol stili erişimin](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) kullanılıp kullanılmayacağını belirtir. İzin verilen değerler: **false** (varsayılan), **true**.<br>Resmi Amazon S3 hizmetinden farklı S3 uyumlu depolama sağlayıcısına bağlanıyorsanız ve bu veri deposu yol stili erişim gerektiriyorsa (örneğin, [Oracle bulut depolaması](https://docs.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)), bu özelliği true olarak ayarlayın. Yol stili erişim gerekliyse, her veri deposunun belgelerini denetleyin. |No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını (veri depolduğunuz özel bir ağda olması halinde) kullanabilirsiniz. Bu özellik belirtilmezse, hizmet varsayılan Azure tümleştirme çalışma zamanını kullanır. |No |

>[!TIP]
>Resmi Amazon S3 hizmeti dışında S3 ile uyumlu bir depolama alanından veri kopyalıyorsanız, özel S3 hizmeti URL 'sini belirtin.

**Örnek: erişim anahtarı kimlik doğrulamasını kullanma**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: geçici güvenlik kimlik bilgisi kimlik doğrulaması kullanma**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
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

Aşağıdaki özellikler, `location` bir biçim tabanlı veri kümesindeki ayarlar bölümünde Amazon S3 için desteklenir:

| Özellik   | Açıklama                                                  | Gerekli |
| ---------- | ------------------------------------------------------------ | -------- |
| tür       |  `location` Bir veri kümesinde bulunan tür özelliği **AmazonS3Location** olarak ayarlanmalıdır. | Yes      |
| bucketName | S3 demet adı.                                          | Yes      |
| folderPath | Verilen demet altındaki klasörün yolu. Klasörü filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirleyin. | No       |
| fileName   | Belirtilen demet ve klasör yolu altındaki dosya adı. Dosyaları filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda belirleyin. | No       |
| sürüm | S3 sürümü etkinse S3 nesnesinin sürümü. Belirtilmemişse, en son sürüm alınacaktır. |No |

**Örnek:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
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

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Amazon S3 kaynağının desteklediği özelliklerin bir listesini sağlar.

### <a name="amazon-s3-as-a-source-type"></a>Kaynak türü olarak Amazon S3

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aşağıdaki özellikler, `storeSettings` bir biçim tabanlı kopyalama kaynağındaki ayarlar bölümünde Amazon S3 için desteklenir:

| Özellik                 | Açıklama                                                  | Gerekli                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| tür                     | İçindeki **tür** özelliği `storeSettings` **AmazonS3ReadSettings** olarak ayarlanmalıdır. | Yes                                                         |
| ***Kopyalanacak dosyaları bulun:*** |  |  |
| SEÇENEK 1: statik yol<br> | Veri kümesinde belirtilen belirli bir demet veya klasör/dosya yolundan kopyalama. Bir demet veya klasörden tüm dosyaları kopyalamak istiyorsanız, ayrıca olarak öğesini belirtin `wildcardFileName` `*` . |  |
| Seçenek 2: S3 ön eki<br>-önek | Kaynak S3 dosyalarını filtrelemek için bir veri kümesinde yapılandırılan belirtilen demet altındaki S3 anahtar adının öneki. Adları ile başlayan S3 anahtarları `bucket_in_dataset/this_prefix` seçilidir. Bir joker karakter filtresinden daha iyi performans sağlayan S3's hizmet tarafı filtresinden yararlanır.<br/><br/>Ön eki ve koruma hiyerarşisi ile dosya tabanlı havuza kopyalamayı seçtiğinizde, önekte son "/" dan sonraki alt yolun korunması gerekir. Örneğin, kaynağı vardır  `bucket/folder/subfolder/file.txt` ve ön eki olarak yapılandırın ve `folder/sub` ardından korunan dosya yolu olur `subfolder/file.txt` . | No |
| Seçenek 3: joker karakter<br>-Yavaya Cardfolderpath | Kaynak klasörleri filtrelemek için bir veri kümesinde yapılandırılan belirtilen demet altında joker karakter olan klasör yolu. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir). `^`Klasör adınızın bir joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. <br>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | No                                            |
| Seçenek 3: joker karakter<br>-Yavaya Cardfilename | Kaynak dosyalarını filtrelemek için, belirtilen demet ve klasör yolu (veya joker karakter klasörü yolu) altındaki joker karakterlerle dosya adı. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir). `^`Dosya adınızın bir joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın.  [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Yes |
| 4. seçenek: dosya listesi<br>-fileListPath | Belirli bir dosya kümesinin kopyalanıp ayrılmadığını gösterir. Veri kümesinde yapılandırılan yolun göreli yolu olan, kopyalamak istediğiniz dosyaların listesini içeren bir metin dosyası üzerine gelin.<br/>Bu seçeneği kullandığınızda, veri kümesinde bir dosya adı belirtmeyin. [Dosya listesi örneklerinde](#file-list-examples)daha fazla örneğe bakın. |No |
| ***Ek ayarlar:*** |  | |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. **Özyinelemeli** değeri **true** olarak ayarlandığında ve havuz dosya tabanlı bir depo olduğunda, havuzda boş bir klasör veya alt klasör kopyalanmadığını veya oluşturulamadığına unutmayın. <br>İzin verilen değerler **true** (varsayılan) ve **false** şeklindedir.<br>Bu özellik, yapılandırdığınızda uygulanmaz `fileListPath` . |No |
| deleteFilesAfterCompletion | Hedef depoya başarıyla taşıdıktan sonra, ikili dosyaların kaynak depodan silinip silinmeyeceğini gösterir. Dosya silme dosya başına yapılır, bu nedenle kopyalama etkinliği başarısız olduğunda, bazı dosyaların hedefe zaten kopyalanmış ve kaynaktan silindiği görürsünüz, diğerleri ise kaynak deposunda hala kalır. <br/>Bu özellik yalnızca ikili dosyalar kopyalama senaryosunda geçerlidir. Varsayılan değer: false. |No |
| modifiedDatetimeStart    | Dosyalar şu özniteliğe göre filtrelenmiştir: son değiştirme. <br>Son değiştirilme zamanı ve arasındaki zaman aralığı içinde ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, "2018-12-01T05:00:00Z" biçiminde bir UTC saat dilimine uygulanır. <br> Özellikler **null** olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacağı anlamına gelir.  Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak **null** ise, son değiştirilen özniteliği DateTime değerinden büyük veya ona eşit olan dosyalar seçilir.  Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak **null** ise, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.<br/>Bu özellik, yapılandırdığınızda uygulanmaz `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Yukarıdakiyle aynıdır.                                               | No                                                          |
| enablePartitionDiscovery | Bölümlenmiş dosyalar için, dosya yolundan bölümlerin ayrıştırıp ayrıştırmayacağını belirtin ve bunları ek kaynak sütunları olarak ekleyin.<br/>İzin verilen değerler **false** (varsayılan) ve **true** şeklindedir. | No                                            |
| Partitionrootyolu | Bölüm bulma etkin olduğunda, bölümlenmiş klasörleri veri sütunları olarak okumak için mutlak kök yolunu belirtin.<br/><br/>Belirtilmemişse, varsayılan olarak<br/>-Veri kümesinde dosya yolunu veya kaynaktaki dosya listesini kullandığınızda, bölüm kök yolu, veri kümesinde yapılandırılan yoldur.<br/>-Joker karakter klasörü filtresi kullandığınızda, bölüm kök yolu ilk joker karakterin öncesindeki alt yoldur.<br/>-Öneki kullandığınızda, bölüm kök yolu son "/" dan önceki alt yoldur. <br/><br/>Örneğin, veri kümesindeki yolu "root/Folder/Year = 2020/ay = 08/gün = 27" olarak yapılandırdığınız varsayılarak:<br/>-Bölüm kök yolunu "root/Folder/Year = 2020" olarak belirtirseniz, kopyalama etkinliği `month` `day` dosyaların içindeki sütunlara ek olarak, sırasıyla "08" ve "27" değeriyle birlikte iki sütun oluşturur.<br/>-Bölüm kök yolu belirtilmemişse, ek sütun oluşturulmaz. | No                                            |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No                                                          |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

| demet | anahtar | öz | Kaynak klasör yapısı ve filtre sonucu (kalın olan dosyalar alınır)|
|:--- |:--- |:--- |:--- |
| demet | `Folder*/*` | yanlış | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| demet | `Folder*/*` | true | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| demet | `Folder*/*.csv` | yanlış | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| demet | `Folder*/*.csv` | true | demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Dosya listesi örnekleri

Bu bölümde, bir kopyalama etkinliği kaynağında dosya listesi yolu kullanmanın ortaya çıkan davranışı açıklanmaktadır.

Aşağıdaki kaynak klasör yapısına sahip olduğunuz ve dosyaları kalın yazı tipinde kopyalamak istediğinizi varsayalım:

| Örnek kaynak yapısı                                      | FileListToCopy.txt içerik                             | Data Factory Yapılandırması                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| demet<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Veriyi<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Veri kümesinde:**<br>Demet `bucket`<br>-Klasör yolu: `FolderA`<br><br>**Kopyalama etkinliği kaynağı:**<br>-Dosya listesi yolu: `bucket/Metadata/FileListToCopy.txt` <br><br>Dosya listesi yolu, veri kümesinde yapılandırılan yolun göreli yolu ile, kopyalamak istediğiniz dosyaların bir listesini, her satıra bir dosya içeren bir metin dosyasını gösterir. |

## <a name="preserve-metadata-during-copy"></a>Kopya sırasında meta verileri koru

Dosyaları Amazon S3 'ten Azure Data Lake Storage 2. veya Azure Blob depolama alanına kopyaladığınızda, dosya meta verilerini verilerle birlikte korumayı seçebilirsiniz. [Meta verileri koru](copy-activity-preserve-metadata.md#preserve-metadata)'dan daha fazla bilgi edinin.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md)inceleyin. 

## <a name="delete-activity-properties"></a>Etkinlik özelliklerini Sil

Özelliklerle ilgili ayrıntıları öğrenmek için [silme etkinliği](delete-activity.md)' ni işaretleyin.

## <a name="legacy-models"></a>Eski modeller

>[!NOTE]
>Geriye dönük uyumluluk için olduğu gibi aşağıdaki modeller de desteklenir. Daha önce bahsedilen yeni modeli kullanmanızı öneririz. Data Factory yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

### <a name="legacy-dataset-model"></a>Eski veri kümesi modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin **Type** özelliği **AmazonS3Object** olarak ayarlanmalıdır. |Yes |
| bucketName | S3 demet adı. Joker karakter filtresi desteklenmiyor. |GetMetadata etkinliği için Hayır, kopyalama veya arama etkinliği için Evet |
| anahtar | Belirtilen demet altındaki S3 nesne anahtarının adı veya joker karakter filtresi. Yalnızca **önek** özelliği belirtilmediğinde geçerlidir. <br/><br/>Joker karakter filtresi, hem klasör bölümü hem de dosya adı bölümü için desteklenir. İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir).<br/>-Örnek 1: `"key": "rootfolder/subfolder/*.csv"`<br/>-Örnek 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örneğe bakın. `^`Gerçek klasörünüz veya dosya adınızın bir joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. |No |
| koy | S3 nesne anahtarı için ön ek. Anahtarları bu önek ile başlayan nesneler seçilidir. Yalnızca **anahtar** özelliği belirtilmediğinde geçerlidir. |No |
| sürüm | S3 sürümü etkinse S3 nesnesinin sürümü. Bir sürüm belirtilmemişse, en son sürüm alınacaktır. |No |
| modifiedDatetimeStart | Dosyalar şu özniteliğe göre filtrelenmiştir: son değiştirme. Son değiştirilme zamanı ve arasındaki zaman aralığı içinde ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Bu ayarın etkinleştirilmesi, çok büyük miktarlarda dosya filtrelemeniz istediğinizde veri hareketinin genel performansını etkiler. <br/><br/> Özellikler **null** olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacağı anlamına gelir.  Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak **null** ise, son değiştirilen özniteliği DateTime değerinden büyük veya ona eşit olan dosyalar seçilir.  Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak null ise, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| No |
| modifiedDatetimeEnd | Dosyalar şu özniteliğe göre filtrelenmiştir: son değiştirme. Son değiştirilme zamanı ve arasındaki zaman aralığı içinde ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Bu ayarın etkinleştirilmesi, çok büyük miktarlarda dosya filtrelemeniz istediğinizde veri hareketinin genel performansını etkiler. <br/><br/> Özellikler **null** olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacağı anlamına gelir.  Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak **null** ise, son değiştirilen özniteliği DateTime değerinden büyük veya ona eşit olan dosyalar seçilir.  Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak **null** ise, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| No |
| biçim | Dosyaları dosya tabanlı depolarla (ikili kopya) olduğu gibi kopyalamak istiyorsanız hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın.<br/><br/>Belirli bir biçimdeki dosyaları ayrıştırmak veya oluşturmak isterseniz, aşağıdaki dosya biçimi türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. **Biçim** ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON biçimi](supported-file-formats-and-compression-codecs-legacy.md#json-format), [avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [orc biçimi](supported-file-formats-and-compression-codecs-legacy.md#orc-format)ve [Parquet biçim](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) bölümleri. |Hayır (yalnızca ikili kopya senaryosu için) |
| sıkıştırma | Verilerin türünü ve sıkıştırma düzeyini belirtin. Daha fazla bilgi için bkz. [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Desteklenen türler **gzip**, **söndür**, **bzip2** ve **zipsöndür**.<br/>Desteklenen düzeyler **en iyi** ve **en hızlardır**. |No |

>[!TIP]
>Bir klasörün altındaki tüm dosyaları kopyalamak için, klasör bölümünün demet ve **ön eki** için **bucketName** belirtin.
>
>Verilen bir ada sahip tek bir dosyayı kopyalamak için, klasör bölümü artı dosya adı için demet ve **anahtar** için **bucketName** belirtin.
>
>Bir klasörün altına bir dosya alt kümesini kopyalamak için, klasör bölümü artı joker karakter filtresi için demet ve **anahtar** için **bucketName** belirtin.

**Örnek: Using öneki**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
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

**Örnek: anahtar ve sürüm kullanma (isteğe bağlı)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>Kopyalama etkinliği için eski kaynak modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının **Type** özelliği **filesystemsource** olarak ayarlanmalıdır. |Yes |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. **Özyinelemeli** değeri **true** olarak ayarlandığında ve havuz dosya tabanlı bir depo olduğunda, havuzda boş bir klasör veya alt klasör kopyalanmayacak veya oluşturulamayacağını unutmayın.<br/>İzin verilen değerler **true** (varsayılan) ve **false** şeklindedir. | No |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
Azure Data Factory içindeki kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
