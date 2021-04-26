---
title: Azure Data Factory kullanarak, 1 ' den veri kopyalama
description: Bir Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak bir buluttan veya şirket içi bir kaynak adına verileri desteklenen havuz veri depolarına kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: 9c274bdfb5854529dbb82bd2d8b7cefdf07390b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588911"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Azure Data Factory kullanarak, bu sunucudan verileri kopyalama

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory hizmetinin sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-hdfs-connector.md)
> * [Güncel sürüm](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Hadoop Dağıtılmış Dosya Sistemi (,) sunucusundan verilerin nasıl kopyalanacağı özetlenmektedir. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Aşağıdaki etkinlikler için,

- [Etkinliği](copy-activity-overview.md) [Desteklenen kaynak ve havuz matrisi](copy-activity-overview.md) ile Kopyala
- [Arama etkinliği](control-flow-lookup-activity.md)
- [Etkinliği sil](delete-activity.md)

Özellikle, bu, şu şekilde destekler:

- *Windows* (Kerberos) veya *anonim* kimlik doğrulaması kullanarak dosya kopyalama.
- *Webbir* protokol veya *yerleşik dıtcp* desteği kullanarak dosyaları kopyalama.
- Dosyaları olarak kopyalama veya [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md)ile dosyaları ayrıştırma veya oluşturma.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Tümleştirme çalışma zamanının tüm [name node Server]: [ad düğümü bağlantı noktası] ve [veri düğüm sunucuları]: [veri düğümü bağlantı noktası] için Hadoop kümesinin *Tüm* erişimine erişebildiğinizden emin olun. Varsayılan [ad düğümü bağlantı noktası] 50070, varsayılan [veri düğümü bağlantı noktası] 50075 ' dir.

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Data Factory 'e özgü varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler,,

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | *Type* özelliği, bir olarak *ayarlanmalıdır.* | Yes |
| url |Bu URL 'nin URL 'si |Yes |
| authenticationType | İzin verilen değerler *anonim* veya *Windows*. <br><br> Şirket içi ortamınızı ayarlamak için, bkz. [bağlantı kimliği Için Kerberos kimlik doğrulaması kullanma](#use-kerberos-authentication-for-the-hdfs-connector) bölümü. |Yes |
| userName |Windows kimlik doğrulaması için Kullanıcı adı. Kerberos kimlik doğrulaması için **\<username> @ \<domain> . com** belirtin. |Evet (Windows kimlik doğrulaması için) |
| password |Windows kimlik doğrulaması için parola. Veri fabrikanıza güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya bir [Azure Anahtar Kasası 'nda depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Evet (Windows kimlik doğrulaması için) |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Daha fazla bilgi edinmek için [Önkoşullar](#prerequisites) bölümüne bakın. Tümleştirme çalışma zamanı belirtilmemişse, hizmet varsayılan Azure Integration Runtime kullanır. |No |

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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [Azure Data Factory veri kümeleri](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aşağıdaki özellikler, `location` Biçim tabanlı veri kümesindeki ayarlar ' ın altında bulunan için desteklenir:

| Özellik   | Açıklama                                                  | Gerekli |
| ---------- | ------------------------------------------------------------ | -------- |
| tür       |  `location` Veri kümesinde bulunan tür özelliğinin *hdfslocation* olarak ayarlanması gerekir. | Yes      |
| folderPath | Klasörün yolu. Klasörü filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları ' nda yolunu belirtin. | No       |
| fileName   | Belirtilen folderPath altındaki dosya adı. Dosyaları filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarlarında dosya adını belirtin. | No       |

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

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için, bkz. [Azure Data Factory Işlem hatları ve etkinlikleri](concepts-pipelines-activities.md). Bu bölüm,,,,

### <a name="hdfs-as-source"></a>Kaynak olarak

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Aşağıdaki özellikler, `storeSettings` Biçim tabanlı kopyalama kaynağındaki ayarlar bölümünde:

| Özellik                 | Açıklama                                                  | Gerekli                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tür                     | Altındaki *Type* özelliği `storeSettings` **hdfsreadsettings** olarak ayarlanmalıdır. | Yes                                           |
| ***Kopyalanacak dosyaları bulun*** |  |  |
| SEÇENEK 1: statik yol<br> | Veri kümesinde belirtilen klasör veya dosya yolundan kopyalama. Tüm dosyaları bir klasörden kopyalamak istiyorsanız, ayrıca olarak öğesini belirtin `wildcardFileName` `*` . |  |
| Seçenek 2: joker karakter<br>-Yavaya Cardfolderpath | Kaynak klasörlerin filtreleneceği joker karakter içeren klasör yolu. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir). `^`Gerçek klasör adınızın bir joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. <br>Daha fazla örnek için bkz. [klasör ve dosya filtresi örnekleri](#folder-and-file-filter-examples). | No                                            |
| Seçenek 2: joker karakter<br>-Yavaya Cardfilename | Kaynak dosyaları filtrelemek için belirtilen folderPath/Yavao Cardfolderpath altındaki joker karakterlerle dosya adı. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); `^` gerçek dosya adınızın bir joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın.  Daha fazla örnek için bkz. [klasör ve dosya filtresi örnekleri](#folder-and-file-filter-examples). | Yes |
| Seçenek 3: dosya listesi<br>-fileListPath | Belirtilen dosya kümesinin kopyalanacağını gösterir. Kopyalamak istediğiniz dosyaların listesini içeren bir metin dosyası üzerine gelin (veri kümesinde yapılandırılan yolun göreli yolu ile her satıra bir dosya).<br/>Bu seçeneği kullandığınızda, veri kümesinde dosya adı belirtmeyin. Daha fazla örnek için bkz. [dosya listesi örnekleri](#file-list-examples). |No |
| ***Ek ayarlar*** |  | |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. `recursive` *Doğru* olarak ayarlandığında ve havuz dosya tabanlı bir deposa, havuzda boş bir klasör veya alt klasör kopyalanmaz veya oluşturulmaz. <br>İzin verilen değerler *true* (varsayılan) ve *false* şeklindedir.<br>Bu özellik, yapılandırdığınızda uygulanmaz `fileListPath` . |No |
| deleteFilesAfterCompletion | Hedef depoya başarıyla taşıdıktan sonra, ikili dosyaların kaynak depodan silinip silinmeyeceğini gösterir. Dosya silme dosya başına yapılır, bu nedenle kopyalama etkinliği başarısız olduğunda, bazı dosyaların hedefe zaten kopyalanmış ve kaynaktan silindiği görürsünüz, diğerleri ise kaynak deposunda hala kalır. <br/>Bu özellik yalnızca ikili dosyalar kopyalama senaryosunda geçerlidir. Varsayılan değer: false. |No |
| modifiedDatetimeStart    | Dosyalar, *son değiştirilen* özniteliğe göre filtrelenir. <br>Son değiştirilme zamanı, ile arasında ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine *2018-12-01T05:00:00Z* biçiminde uygulanır. <br> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir.  Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak null ise, son değiştirilen özniteliği DateTime değerinden büyük veya buna eşit olan dosyaların seçildiği anlamına gelir.  Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak null ise, son değiştirilen özniteliği tarih saat değerinden küçük olan dosyaların seçildiği anlamına gelir.<br/>Bu özellik, yapılandırdığınızda uygulanmaz `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Yukarıdakiyle aynıdır.  
| enablePartitionDiscovery | Bölümlenmiş dosyalar için, dosya yolundan bölümlerin ayrıştırıp ayrıştırmayacağını belirtin ve bunları ek kaynak sütunları olarak ekleyin.<br/>İzin verilen değerler **false** (varsayılan) ve **true** şeklindedir. | No                                            |
| Partitionrootyolu | Bölüm bulma etkin olduğunda, bölümlenmiş klasörleri veri sütunları olarak okumak için mutlak kök yolunu belirtin.<br/><br/>Belirtilmemişse, varsayılan olarak<br/>-Veri kümesinde dosya yolunu veya kaynaktaki dosya listesini kullandığınızda, bölüm kök yolu, veri kümesinde yapılandırılan yoldur.<br/>-Joker karakter klasörü filtresi kullandığınızda, bölüm kök yolu ilk joker karakterin öncesindeki alt yoldur.<br/><br/>Örneğin, veri kümesindeki yolu "root/Folder/Year = 2020/ay = 08/gün = 27" olarak yapılandırdığınız varsayılarak:<br/>-Bölüm kök yolunu "root/Folder/Year = 2020" olarak belirtirseniz, kopyalama etkinliği `month` `day` dosyaların içindeki sütunlara ek olarak, sırasıyla "08" ve "27" değeriyle birlikte iki sütun oluşturur.<br/>-Bölüm kök yolu belirtilmemişse, ek sütun oluşturulmaz. | No                                            |
| maxConcurrentConnections | Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No                                            |
| ***DistCp ayarları*** |  | |
| distcpSettings | ,,,, | No |
| resourceManagerEndpoint | YARN (ancak başka bir kaynak Negotiator) uç noktası | Evet, DistCp kullanılıyorsa |
| tempScriptPath | Temp DistCp komut betiğini depolamak için kullanılan bir klasör yolu. Betik dosyası Data Factory tarafından oluşturulur ve kopyalama işi tamamlandıktan sonra kaldırılır. | Evet, DistCp kullanılıyorsa |
| distcpOptions | DistCp komutuna ek seçenekler sağlanmaktadır. | No |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Klasör ve dosya filtresi örnekleri

Bu bölümde, klasör yolu ve dosya adı ile bir joker karakter filtresi kullanıyorsanız ortaya çıkan davranış açıklanmaktadır.

| folderPath | fileName             | öz | Kaynak klasör yapısı ve filtre sonucu ( **kalın** olan dosyalar alınır) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (boş, Varsayılanı kullan) | yanlış     | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (boş, Varsayılanı kullan) | true      | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | yanlış     | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Dosya listesi örnekleri

Bu bölümde, kopyalama etkinlik kaynağında bir dosya listesi yolu kullanmanın sonucu olan davranış açıklanmaktadır. Aşağıdaki kaynak klasör yapısına sahip olduğunuzu ve kalın türdeki dosyaları kopyalamak istediğinizi varsayar:

| Örnek kaynak yapısı                                      | FileListToCopy.txt içerik                             | Azure Data Factory Yapılandırması                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| kök<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Veriyi<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Veri kümesinde:**<br>-Klasör yolu: `root/FolderA`<br><br>**Kopyalama etkinliği kaynağı:**<br>-Dosya listesi yolu: `root/Metadata/FileListToCopy.txt` <br><br>Dosya listesi yolu, kopyalamak istediğiniz dosyaların listesini içeren aynı veri deposundaki bir metin dosyasına işaret eder (veri kümesinde yapılandırılan yolun göreli yolu ile her satıra bir dosya). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Sunucudan verileri kopyalamak için DistCp kullanma

[Distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) , Hadoop kümesinde dağıtılmış bir kopya yapmak Için bir Hadoop Native komut satırı aracıdır. DistCp 'de bir komutu çalıştırdığınızda, önce kopyalanacak tüm dosyaları listeler ve ardından Hadoop kümesinde birkaç eşleme işi oluşturur. Her eşleme işi kaynaktan havuza bir ikili kopya yapar.

Kopyalama etkinliği, dosyaları Azure Blob depolama alanına ( [hazırlanan kopya](copy-activity-performance.md)dahil) veya bir Azure Data Lake Store 'a olduğu gibi kopyalamak Için distcp kullanımını destekler. Bu durumda, DistCp, kendinden konak tümleştirme çalışma zamanı üzerinde çalıştırmak yerine kümenizin gücünden yararlanabilir. DistCp kullanmak, özellikle kümeniz çok güçlü olduğunda daha iyi kopyalama performansı sağlar. Veri fabrikanızdaki yapılandırmaya bağlı olarak, kopyalama etkinliği otomatik olarak bir DistCp komutu oluşturur, bunu Hadoop kümenize gönderir ve kopyalama durumunu izler.

### <a name="prerequisites"></a>Önkoşullar

Dosya kopyalamak için DistCp 'yi kullanmak için, Azure Blob depolama 'dan (hazırlanan kopya dahil) veya Azure Data Lake Store 'dan Azure Blob Storage 'dan, Hadoop kümenizin aşağıdaki gereksinimleri karşıladığından emin olun:

* MapReduce ve YARN Hizmetleri etkindir.  
* YARN sürümü 2,5 veya üzeri bir sürüm.  
* Bu sunucu hedef veri deponuzda tümleşiktir: **Azure Blob depolama** veya **Azure Data Lake Store (ADLS 1.)**: 

    - Azure Blob FileSystem, Hadoop 2,7 ' den bu yana yerel olarak desteklenir. Yalnızca Hadoop ortam yapılandırmasında JAR yolunu belirtmeniz gerekir.
    - Azure Data Lake Store dosya sistemi, Hadoop 3.0.0-Alpha1 'den başlayarak paketlenir. Hadoop kümeniz sürümü Bu sürümden daha eski ise, Azure Data Lake Store ilgili JAR paketlerini (Azure-datalake-Store. jar) [buradan](https://hadoop.apache.org/releases.html)kümeye el ile aktarmanız ve Hadoop ortam yapılandırmasında jar dosya yolunu belirtmeniz gerekir.

* Bir Temp klasörünü, "bir" de hazırlayın. Bu Temp klasörü, bir DistCp kabuğu betiğini depolamak için kullanılır, bu nedenle KB düzeyinde alan kaplar.
* ,,,,, Bağlantı hizmeti 'nde belirtilen kullanıcı hesabının şu izinlere sahip olduğundan emin olun:
   * YARN 'de bir uygulama gönderme.
   * Geçici klasör altında bir alt klasör oluşturun ve dosyaları okur/yazın.

### <a name="configurations"></a>Yapılandırmalar

TCP ile ilgili olmayan konfigürasyonlar ve örnekler için, [kaynak olarak](#hdfs-as-source) ,, kaynak bölümüne gidin.

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Bağlantı kimliği için Kerberos kimlik doğrulaması kullanma

Şirket içi ortamı, IBir bağlantı için Kerberos kimlik doğrulaması kullanacak şekilde ayarlamaya yönelik iki seçenek vardır. Durumunuza daha uygun olanı seçebilirsiniz.
* Seçenek 1: [Kerberos bölgesindeki şirket içinde barındırılan tümleştirme çalışma zamanı makinesine ekleme](#kerberos-join-realm)
* 2. seçenek: [Windows etki alanı ve Kerberos bölgesi arasında karşılıklı güveni etkinleştirin](#kerberos-mutual-trust)

Her iki seçenek için de bkz. Hadoop kümesi için Web 'i etkinleştirdiğinizden emin olun:

1. Web için HTTP sorumlusu ve keytab oluşturma.

    > [!IMPORTANT]
    > HTTP Kerberos sorumlusu, Kerberos HTTP SPNEGO belirtimine göre "**http/**" ile başlamalıdır. [Buradan](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options)daha fazla bilgi edinebilirsiniz.

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. Bu yapılandırma seçenekleri: ' de aşağıdaki üç özelliği ekleyin `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Seçenek 1: Kerberos bölgesindeki şirket içinde barındırılan tümleştirme çalışma zamanı makinesine ekleme

#### <a name="requirements"></a>Gereksinimler

* Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinin Kerberos bölgesine katılması ve herhangi bir Windows etki alanına katılamıyor olması gerekir.

#### <a name="how-to-configure"></a>Yapılandırma

**KDC sunucusunda:**

Kullanılacak Azure Data Factory için bir sorumlu oluşturun ve parolayı belirtin.

> [!IMPORTANT]
> Kullanıcı adı, ana bilgisayar adını içermemelidir.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde:**

1.  Kerberos Anahtar Dağıtım Merkezi (KDC) sunucusunu ve bölgeyi yapılandırmak için Ksetup yardımcı programını çalıştırın.

    Bir Kerberos bölgesi bir Windows etki alanından farklı olduğundan, makinenin bir çalışma grubunun üyesi olarak yapılandırılması gerekir. Aşağıdaki komutları çalıştırarak, Kerberos bölgesini ayarlayıp bir KDC sunucusu ekleyerek bu yapılandırmayı elde edebilirsiniz. *Realm.com* değerini kendi bölge adınızla değiştirin.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Bu komutları çalıştırdıktan sonra makineyi yeniden başlatın.

2.  Komutu ile yapılandırmayı doğrulayın `Ksetup` . Çıktının şöyle olması gerekir:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**Veri fabrikasında:**

* Windows kimlik doğrulamasını kullanarak, bu TBU veri kaynağına bağlanmak için Kerberos asıl adınız ve parolanızla birlikte, Windows kimlik doğrulaması 'nı kullanarak Yapılandırma ayrıntıları için, bu [bağlantı hizmeti özellikleri](#linked-service-properties) bölümüne bakın.

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>2. seçenek: Windows etki alanı ve Kerberos bölgesi arasında karşılıklı güveni etkinleştirin

#### <a name="requirements"></a>Gereksinimler

*   Şirket içinde barındırılan tümleştirme çalışma zamanı makinesi bir Windows etki alanına katılması gerekir.
*   Etki alanı denetleyicisinin ayarlarını güncelleştirmek için izninizin olması gerekir.

#### <a name="how-to-configure"></a>Yapılandırma

> [!NOTE]
> Aşağıdaki öğreticide yer alan REALM.COM ve AD.COM değerini kendi bölge adınızla ve etki alanı denetleyiciyle değiştirin.

**KDC sunucusunda:**

1. Aşağıdaki yapılandırma şablonuna başvurarak KDC 'nin Windows etki alanına güvenmesini sağlamak için *krb5. conf* dosyasındaki KDC yapılandırmasını düzenleyin. Varsayılan olarak, yapılandırma */etc/kronb5,conf* konumunda bulunur.

   ```config
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
    ```

   Dosyayı yapılandırdıktan sonra, KDC hizmetini yeniden başlatın.

2. Aşağıdaki komutla, KDC sunucusunda *krbtgt/Realm. COM \@ ad.com* adlı bir sorumlu hazırlayın:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. *Hadoop.Security.auth_to_local* , hizmet yapılandırma dosyasında, öğesini ekleyin `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Etki alanı denetleyicisinde:**

1.  `Ksetup`Bir bölge girişi eklemek için aşağıdaki komutları çalıştırın:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Windows etki alanından Kerberos bölgesine güven oluşturun. [password] sorumlu *krbtgt/Realm. COM \@ ad.com* için paroladır.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Kerberos 'da kullanılan şifreleme algoritmasını seçin.

    a. **Sunucu Yöneticisi**  >  **Grup İlkesi Yönetim**  >  **etki alanı**  >  **Grup İlkesi nesneleri**  >  **varsayılan veya etkin etki alanı ilkesi**' ni seçin ve ardından **Düzenle**' yi seçin

    b. **Grup İlkesi Yönetimi Düzenleyicisi** bölmesinde **bilgisayar yapılandırma**  >  **ilkeleri**  >  **Windows ayarları**  >  **güvenlik ayarları**  >  **Yerel ilkeler**  >  **güvenlik seçenekleri**' ni seçin ve ardından **ağ güvenliğini yapılandırın: Kerberos için izin verilen şifreleme türlerini yapılandırın**.

    c. KDC sunucusuna bağlanırken kullanmak istediğiniz şifreleme algoritmasını seçin. Tüm seçenekleri belirleyebilirsiniz.

    !["Ağ güvenliği: Kerberos için izin verilen şifreleme türlerini yapılandırma" panosu ekran görüntüsü](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Belirtilen bölgede `Ksetup` kullanılacak şifreleme algoritmasını belirtmek için komutunu kullanın.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Windows etki alanında Kerberos sorumlusunu kullanabilmeniz için etki alanı hesabı ve Kerberos sorumlusu arasında eşleme oluşturun.

    a.   >  **Kullanıcılar ve bilgisayarlar Active Directory** Yönetim Araçları ' nı seçin.

    b. Gelişmiş özellikleri **görüntüle**  >  **Gelişmiş** özelliklerini seçerek yapılandırın.

    c. **Gelişmiş Özellikler** bölmesinde, eşleme oluşturmak istediğiniz hesaba sağ tıklayın ve **ad eşlemeleri** bölmesinde **Kerberos adları** sekmesini seçin.

    d. Realm 'tan bir sorumlu ekleyin.

       !["Güvenlik kimliği eşleme" bölmesi](media/connector-hdfs/map-security-identity.png)

**Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde:**

* `Ksetup`Bir bölge girişi eklemek için aşağıdaki komutları çalıştırın.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Veri fabrikasında:**

* Windows kimlik doğrulamasını, etki alanı hesabınızla veya Kerberos sorumlusuyla birlikte, bir TBU veri kaynağına bağlanmak için kullanarak, bu bağlantı kimliğini yapılandırın. Yapılandırma ayrıntıları için, bkz. [bağlantı hizmeti özellikleri](#linked-service-properties) bölümü.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Arama etkinliği özellikleri hakkında daha fazla bilgi için [Azure Data Factory arama etkinliği](control-flow-lookup-activity.md)bölümüne bakın.

## <a name="delete-activity-properties"></a>Etkinlik özelliklerini Sil

Etkinlik özelliklerini silme hakkında daha fazla bilgi için bkz. [Azure Data Factory etkinliği silme](delete-activity.md).

## <a name="legacy-models"></a>Eski modeller

>[!NOTE]
>Geriye dönük uyumluluk için olduğu gibi aşağıdaki modeller de desteklenir. Azure Data Factory yazma Kullanıcı arabirimi yeni modeli oluşturmak üzere değiştiğinden, daha önce tartışılan yeni modeli kullanmanızı öneririz.

### <a name="legacy-dataset-model"></a>Eski veri kümesi modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in *Type* özelliği *FileShare* olarak ayarlanmalıdır |Yes |
| folderPath | Klasörün yolu. Joker karakter filtresi desteklenir. İzin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); `^` gerçek dosya adınızın bir joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. <br/><br/>Örnekler: RootFolder/alt klasör/, bkz. [klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek. |Yes |
| fileName |  Belirtilen "folderPath" altındaki dosyalar için ad veya joker karakter filtresi. Bu özellik için bir değer belirtmezseniz, veri kümesi klasördeki tüm dosyaları işaret eder. <br/><br/>Filtre için, izin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek bir karakterle eşleşir).<br/>-Örnek 1: `"fileName": "*.csv"`<br/>-Örnek 2: `"fileName": "???20180427.txt"`<br/>`^`Gerçek klasör adınızın bir joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. |No |
| modifiedDatetimeStart | Dosyalar, *son değiştirilen* özniteliğe göre filtrelenir. Son değiştirilme zamanı, ile arasında ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine *2018-12-01T05:00:00Z* biçiminde uygulanır. <br/><br/> Çok sayıda dosyaya dosya filtresi uygulamak istediğinizde bu ayarın etkinleştirilmesi için veri hareketinin genel performansının etkileneceği göz önünde bulundurun. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir.  Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak null ise, son değiştirilen özniteliği DateTime değerinden büyük veya buna eşit olan dosyaların seçildiği anlamına gelir.  Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak null ise, son değiştirilen özniteliği tarih saat değerinden küçük olan dosyaların seçildiği anlamına gelir.| No |
| modifiedDatetimeEnd | Dosyalar, *son değiştirilen* özniteliğe göre filtrelenir. Son değiştirilme zamanı, ile arasında ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine *2018-12-01T05:00:00Z* biçiminde uygulanır. <br/><br/> Çok sayıda dosyaya dosya filtresi uygulamak istediğinizde bu ayarın etkinleştirilmesi için veri hareketinin genel performansının etkileneceği göz önünde bulundurun. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir.  Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak null ise, son değiştirilen özniteliği DateTime değerinden büyük veya buna eşit olan dosyaların seçildiği anlamına gelir.  Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak null ise, son değiştirilen özniteliği tarih saat değerinden küçük olan dosyaların seçildiği anlamına gelir.| No |
| biçim | Dosyaları dosya tabanlı depolarla (ikili kopya) olduğu gibi kopyalamak istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın.<br/><br/>Dosyaları belirli bir biçimde ayrıştırmak isterseniz, aşağıdaki dosya biçimi türleri desteklenir: *TextFormat*, *jsonformat*, *avroformat*, *orcformat*, *parquetformat*. Biçim ' in altındaki *Type* özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON biçimi](supported-file-formats-and-compression-codecs-legacy.md#json-format), [avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [orc biçimi](supported-file-formats-and-compression-codecs-legacy.md#orc-format)ve [Parquet biçim](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) bölümleri. |Hayır (yalnızca ikili kopya senaryosu için) |
| sıkıştırma | Verilerin türünü ve sıkıştırma düzeyini belirtin. Daha fazla bilgi için bkz. [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Desteklenen türler şunlardır: *gzip*, *söndür*, *bzip2* ve *zipsöndür*.<br/>Desteklenen düzeyler şunlardır: *en iyi* ve *en hızlı*. |No |

>[!TIP]
>Bir klasörün altındaki tüm dosyaları kopyalamak için, yalnızca **FolderPath** ' i belirtin.<br>Belirtilen ada sahip tek bir dosyayı kopyalamak için klasör **bölümü ve dosya adı ile dosya** adı ile **FolderPath** belirtin.<br>Bir klasörün altına bir dosya alt kümesini kopyalamak için klasör bölümü ve **dosya adı** joker karakter filtresi ile **FolderPath** öğesini belirtin.

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

### <a name="legacy-copy-activity-source-model"></a>Eski kopyalama etkinliği kaynak modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının *Type* özelliği *hdfssource* olarak ayarlanmalıdır. |Yes |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri *true* olarak ayarlandığında ve havuz dosya tabanlı bir deposa, boş bir klasör veya alt klasör, havuzda kopyalanmaz veya oluşturulmaz.<br/>İzin verilen değerler *true* (varsayılan) ve *false* şeklindedir. | No |
| distcpSettings | Bu özellik grubu, bir. | No |
| resourceManagerEndpoint | YARN Kaynak Yöneticisi uç noktası | Evet, DistCp kullanılıyorsa |
| tempScriptPath | Temp DistCp komut betiğini depolamak için kullanılan bir klasör yolu. Betik dosyası Data Factory tarafından oluşturulur ve kopyalama işi tamamlandıktan sonra kaldırılır. | Evet, DistCp kullanılıyorsa |
| distcpOptions | DistCp komutuna ek seçenekler sağlanır. | No |
| maxConcurrentConnections | Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

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

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
