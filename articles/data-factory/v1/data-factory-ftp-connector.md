---
title: Azure Data Factory kullanarak bir FTP sunucusundan veri taşıma
description: Azure Data Factory kullanarak FTP sunucusundan veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5a6485e93e8f617883a7dfef511709ec857b411
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682589"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Azure Data Factory kullanarak bir FTP sunucusundan veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-ftp-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-ftp.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de FTP Bağlayıcısı](../connector-ftp.md).

Bu makalede, bir FTP sunucusundan verileri taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

FTP sunucusundan desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca bir FTP sunucusundan diğer veri depolarına veri taşımayı destekler, ancak diğer veri depolarından verileri bir FTP sunucusuna taşımamaktadır. Hem şirket içi hem de bulut FTP sunucularını destekler.

> [!NOTE]
> Kopyalama etkinliği, hedefe başarıyla kopyalandıktan sonra kaynak dosyayı silmez. Başarılı bir kopyadan sonra kaynak dosyayı silmeniz gerekiyorsa, dosyayı silmek için özel bir etkinlik oluşturun ve işlem hattındaki etkinliğini kullanın.

## <a name="enable-connectivity"></a>Bağlantıyı etkinleştir
**Şirket içi** bir FTP sunucusundan verileri bir bulut veri deposuna (örneğin, Azure Blob depolama alanına) taşıyorsanız, veri yönetimi ağ geçidini yükleyip kullanın. Veri Yönetimi ağ geçidi, şirket içi makinenize yüklenmiş bir istemci aracısıdır ve bulut hizmetlerinin şirket içi bir kaynağa bağlanmasına izin verir. Ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md). Ağ geçidini ayarlamaya ve kullanmaya ilişkin adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md). Sunucu, Azure hizmet olarak altyapı (IaaS) sanal makinesi (VM) üzerinde olsa bile, bir FTP sunucusuna bağlanmak için ağ geçidini kullanırsınız.

Ağ geçidini, FTP sunucusu ile aynı şirket içi makineye veya IaaS VM 'sine yüklemek mümkündür. Ancak, kaynak çekişmesini önlemek ve daha iyi performans sağlamak için ağ geçidini ayrı bir makineye veya IaaS VM 'sine yüklemenizi öneririz. Ağ geçidini ayrı bir makineye yüklediğinizde, makine FTP sunucusuna erişebilmelidir.

## <a name="get-started"></a>Başlarken
Farklı araçları veya API 'Leri kullanarak bir FTP kaynağından veri taşıyan kopyalama etkinliğiyle bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Data Factory kopyalama Sihirbazı**' nı kullanmaktır. Hızlı bir yol için bkz. [öğretici: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirin:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçları veya API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Bir FTP veri deposundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarına sahip bir örnek için, bu makaledeki [JSON örneği: FTP sunucusundan Azure Blob 'a veri kopyalama](#json-example-copy-data-from-ftp-server-to-azure-blob) bölümüne bakın.

> [!NOTE]
> Kullanılacak desteklenen dosya ve sıkıştırma biçimleri hakkında daha fazla bilgi için, bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md).

Aşağıdaki bölümler, FTP 'ye özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, bir FTP bağlantılı hizmetine özgü JSON öğeleri açıklanmaktadır.

| Özellik | Açıklama | Gerekli | Varsayılan |
| --- | --- | --- | --- |
| type |Bunu FtpServer olarak ayarlayın. |Evet |&nbsp; |
| Konağının |FTP sunucusunun adını veya IP adresini belirtin. |Evet |&nbsp; |
| authenticationType |Kimlik doğrulama türünü belirtin. |Evet |Temel, anonim |
| kullanıcı adı |FTP sunucusuna erişimi olan kullanıcıyı belirtin. |Hayır |&nbsp; |
| password |Kullanıcının parolasını belirtin (Kullanıcı adı). |Hayır |&nbsp; |
| encryptedCredential |FTP sunucusuna erişmek için şifrelenmiş kimlik bilgisini belirtin. |Hayır |&nbsp; |
| gatewayName |Şirket içi FTP sunucusuna bağlanmak için Veri Yönetimi ağ geçidinde ağ geçidinin adını belirtin. |Hayır |&nbsp; |
| port |FTP sunucusunun dinlediği bağlantı noktasını belirtin. |Hayır |21 |
| enableSsl |Bir SSL/TLS kanalı üzerinden FTP kullanılıp kullanılmayacağını belirtin. |Hayır |doğru |
| enableServerCertificateValidation |SSL/TLS kanalı üzerinden FTP kullanırken sunucu SSL sertifikası doğrulamasının etkinleştirilip etkinleştirilmeyeceğini belirtin. |Hayır |doğru |

>[!NOTE]
>FTP Bağlayıcısı, şifreleme veya açık SSL/TLS şifrelemesi olmadan FTP sunucusuna erişmeyi destekler; örtük SSL/TLS şifrelemesini desteklemez.

### <a name="use-anonymous-authentication"></a>Anonim kimlik doğrulaması kullan

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Basit kimlik doğrulaması için Kullanıcı adı ve parolayı düz metin olarak kullan

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Bağlantı noktası, enableSsl, enableServerCertificateValidation kullanın

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Kimlik doğrulaması ve ağ geçidi için encryptedCredential kullanın

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md). Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır. Veri kümesi türüne özgü bilgiler sağlar. **FileShare** türünde bir veri kümesi Için **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasöre alt yol. Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örnekler için bkz. örnek bağlantılı hizmet ve veri kümesi tanımları.<br/><br/>Bu özelliği, dilim başlangıç ve bitiş tarih zamanları temelinde klasör yollarına sahip olmak için **Partitionby** ile birleştirebilirsiniz. |Evet |
| fileName |Tablonun klasördeki belirli bir dosyaya başvurmasını istiyorsanız, **FolderPath** içindeki dosyanın adını belirtin. Bu özellik için herhangi bir değer belirtmezseniz tablo, klasördeki tüm dosyaları gösterir.<br/><br/>Bir çıkış veri kümesi için **dosya adı** belirtilmediğinde, oluşturulan dosyanın adı şu biçimdedir: <br/><br/>`Data.<Guid>.txt` (örnek: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt) |Hayır |
| fileFilter |Tüm dosyalar yerine **FolderPath**içindeki dosyaların bir alt kümesini seçmek için kullanılacak bir filtre belirtin.<br/><br/>İzin verilen değerler: `*` (birden fazla karakter) ve `?` (tek karakter).<br/><br/>Örnek 1: `"fileFilter": "*.log"`<br/>Örnek 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **FileFilter** , bir giriş FileShare veri kümesi için geçerlidir. Bu özellik Hadoop Dağıtılmış Dosya Sistemi (bir) ile desteklenmiyor. |Hayır |
| partitionedBy |Zaman serisi verileri için dinamik bir **FolderPath** ve **filename** belirtmek için kullanılır. Örneğin, her saat veri için parametreli bir **FolderPath** belirtebilirsiniz. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında olduğu gibi kopyalamak istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler **gzip**, **söndür**, **bzip2**ve **zipsöndür**ve desteklenen düzeyler **en iyi** ve **en hızlardır**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |
| useBinaryTransfer |İkili aktarım modunun kullanılıp kullanılmayacağını belirtin. Değerler ikili mod için (varsayılan değerdir), ASCII için false 'dur. Bu özellik, yalnızca ilişkili bağlı hizmet türü: FtpServer türünde olduğunda kullanılabilir. |Hayır |

> [!NOTE]
> **filename** ve **FileFilter** aynı anda kullanılamaz.

### <a name="use-the-partionedby-property"></a>PartionedBy özelliğini kullanma
Önceki bölümde belirtildiği gibi, **Partitionedby** özelliği ile zaman serisi verileri için dinamik bir **FolderPath** ve **filename** belirtebilirsiniz.

Zaman serisi veri kümeleri, zamanlama ve dilimler hakkında bilgi edinmek için bkz. [veri kümeleri](data-factory-create-datasets.md), [zamanlama ve yürütme](data-factory-scheduling-and-execution.md)ve işlem [hatları oluşturma](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Örnek 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Bu örnekte, {Slice}, belirtilen biçimde (YYYYMMDDHH) Data Factory sistem değişkeni Dilimestart değeriyle değiştirilmiştir. Dilimcstart, dilimin başlangıç zamanına başvurur. Klasör yolu her bir dilim için farklıdır. (Örneğin, wikidatagateway/wisvahili amptadataout/2014100103 veya wikidatagateway/wisvahili amptadataout/2014100104.)

#### <a name="sample-2"></a>Örnek 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Bu örnekte, monthestart 'ın Year, month, Day ve saati, **FolderPath** ve **filename** özellikleri tarafından kullanılan ayrı değişkenlere ayıklanır.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md). Ad, açıklama, giriş ve çıkış tabloları ve ilkeler gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Etkinliğin **Typeproperties** bölümünde bulunan özellikler, diğer yandan her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için tür özellikleri, kaynak ve havuz türlerine göre değişir.

Kopyalama etkinliğinde, kaynak **Filesystemsource**türünde olduğunda aşağıdaki özellik **typeproperties** bölümünde mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True, false (varsayılan) |Hayır |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON örneği: FTP sunucusundan Azure Blob 'a veri kopyalama
Bu örnek, bir FTP sunucusundan Azure Blob depolamaya nasıl veri kopyalanacağını gösterir. Ancak, veriler, Data Factory içindeki kopyalama etkinliği kullanılarak [desteklenen veri depoları ve biçimlerinde](data-factory-data-movement-activities.md#supported-data-stores-and-formats)belirtilen herhangi bir havuza doğrudan kopyalanabilir.

Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)veya [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar:

* [FtpServer](#linked-service-properties) türünde bağlı bir hizmet
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties) türünde bağlı bir hizmet
* [FileShare](#dataset-properties) türünde bir giriş [veri kümesi](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) türünde bir çıkış [veri kümesi](data-factory-create-datasets.md)
* [Filesystemsource](#copy-activity-properties) ve [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md)

Örnek, verileri bir FTP sunucusundan her saat bir Azure blobuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

### <a name="ftp-linked-service"></a>FTP bağlı hizmeti

Bu örnek, basit kimlik doğrulamasını, Kullanıcı adı ve parolasıyla düz metin olarak kullanır. Aşağıdaki yollarla da kullanabilirsiniz:

* Anonim kimlik doğrulaması
* Şifrelenmiş kimlik bilgileriyle temel kimlik doğrulaması
* SSL/TLS üzerinden FTP (FTPS)

Kullanabileceğiniz farklı kimlik doğrulama türleri için [FTP bağlı hizmeti](#linked-service-properties) bölümüne bakın.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
### <a name="ftp-input-dataset"></a>FTP giriş veri kümesi

Bu veri kümesi, `mysharedfolder` FTP klasörünü ve dosya `test.csv`anlamına gelir. İşlem hattı, dosyayı hedefe kopyalar.

**External** to **true** olarak ayarlamak, Data Factory hizmetine veri kümesinin veri fabrikası dışında olduğunu bildirir ve veri fabrikasında bir etkinlik tarafından üretilmez.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob çıktı veri kümesi

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Dosya sistemi kaynağına ve BLOB havuzuna sahip bir işlem hattındaki kopyalama etkinliği

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **filesystemsource**olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

* Veri taşıma (kopyalama etkinliği) performansını Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md).

* Kopyalama etkinliği ile işlem hattı oluşturmaya yönelik adım adım yönergeler için, [kopyalama etkinliği öğreticisine](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)bakın.
