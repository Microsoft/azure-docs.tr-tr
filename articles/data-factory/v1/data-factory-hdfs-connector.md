---
title: Verileri şirket içi HDFS taşıma | Microsoft Docs
description: Azure Data Factory kullanarak şirket içi HDFS veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f28c7b94a9eb8131f0638a24a0d4b3cfccf062e5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836288"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Azure Data Factory kullanarak şirket içi hdfs veri taşıma
> [!div class="op_single_selector" title1="Data Factory hizmetinin kullandığınız sürümü seçin:"]
> * [Sürüm 1](data-factory-hdfs-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-hdfs.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz [V2 HDFS bağlayıcısında](../connector-hdfs.md).

Bu makalede, bir şirket içi HDFS verileri taşımak için Azure Data Factory kopyalama etkinliği kullanmayı açıklar. Yapılar [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi, kopyalama etkinliği ile verileri taşıma genel bir bakış sunar.

Verileri HDFS tüm desteklenen havuz veri deposuna kopyalayabilirsiniz. Havuz kopyalama etkinliği tarafından desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablo. Data factory şu anda yalnızca verileri şirket içi HDFS'ye verileri diğer veri depolarına bir şirket içi HDFS'ye taşımak için değil ancak diğer veri depolarını destekler.

> [!NOTE]
> Hedefine başarıyla kopyalandıktan sonra kopyalama etkinliği kaynak dosya silinmez. Kopyalama başarılı sonra kaynak dosyayı silmek için ihtiyacınız varsa dosyayı silin ve işlem hattı, etkinlik kullanmak için özel bir etkinlik oluşturun. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Bağlantıyı etkinleştirme
Data Factory hizmeti, veri yönetimi ağ geçidi kullanarak şirket içi HDFS'ye bağlanmayı destekler. Bkz: [Bulut ve şirket içi konumlar arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalenin veri yönetimi ağ geçidi ve ağ geçidini ayarlamadan adım adım yönergeleri hakkında bilgi edinin. Bir Azure Iaas VM'de barındırılıyor olsa bile HDFS'ye bağlanmak için ağ geçidi'ni kullanın.

> [!NOTE]
> Emin veri yönetimi ağ geçidi için erişebileceği **tüm** [ad sunucusu düğümü]: [ad, düğüm bağlantı noktası] ve [veri düğümü sunucuları]: [veri düğümü bağlantı noktası] Hadoop kümesi. 50070 varsayılan [ad düğümü bağlantı noktası] ve [veri düğümü bağlantı noktası] 50075 varsayılandır.

Aynı şirket içi makinede veya HDFS olarak Azure VM ağ geçidi yükleme sırasında bir ayrı makine/Azure Iaas VM ağ geçidi yüklemeniz önerilir. Ağ geçidi, ayrı bir makineye sahip olmak, kaynak çekişmesini azaltır ve performansı geliştirir. Ağ geçidi ayrı bir makineye yüklediğinizde, makine HDFS makineyle erişebilir olmalıdır.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API'lerini kullanarak bir HDFS kaynaktan veri taşıyan kopyalama etkinliği ile işlem hattı oluşturabilirsiniz.

Bir işlem hattı oluşturmanın en kolay yolu kullanmaktır **Kopyalama Sihirbazı'nı**. Bkz: [Öğreticisi: Kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) veri kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma hızlı bir kılavuz.

Ayrıca, bir işlem hattı oluşturmak için aşağıdaki araçları kullanabilirsiniz: **Azure portalında**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve  **REST API**. Bkz: [kopyalama etkinliği Öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kopyalama etkinliği ile işlem hattı oluşturmak adım adım yönergeler için.

API'ler ve Araçlar kullanmanıza bakılmaksızın, bir havuz veri deposu için bir kaynak veri deposundan veri taşıyan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirin:

1. Oluşturma **bağlı hizmetler** girdi ve çıktı verilerini bağlamak için veri fabrikanıza depolar.
2. Oluşturma **veri kümeleri** kopyalama işleminin girdi ve çıktı verilerini göstermek için.
3. Oluşturma bir **işlem hattı** bir veri kümesini girdi ve çıktı olarak bir veri kümesini alan kopyalama etkinliği ile.

Sihirbazı'nı kullandığınızda, bu Data Factory varlıklarını (bağlı hizmetler, veri kümeleri ve işlem hattı) için JSON tanımları sizin için otomatik olarak oluşturulur. Araçlar/API'leri (dışında .NET API'si) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlayın.  Bir HDFS veri deposundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları ile bir örnek için bkz. [JSON örneği: Verileri Azure Blob için şirket içi HDFS kopyalama](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) bu makalenin.

Aşağıdaki bölümler, Data Factory varlıklarını belirli HDFS'ye tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri
Bağlı hizmet, bir veri deposuna bir veri fabrikasına bağlar. Bağlı hizmet türü oluşturma **Hdfs** bir şirket içi HDFS'ye veri fabrikanıza bağlamak için. Aşağıdaki tabloda, bağlı hizmeti HDFS'ye özgü JSON öğeleri için bir açıklama sağlar.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| türü |Type özelliği ayarlanmalıdır: **Hdfs** |Evet |
| url |HDFS URL'si |Evet |
| authenticationType |Anonim veya Windows. <br><br> Kullanılacak **Kerberos kimlik doğrulaması** HDFS bağlayıcısının başvurmak [Bu bölümde](#use-kerberos-authentication-for-hdfs-connector) şirket içi ortamınızı uygun şekilde ayarlamak için. |Evet |
| userName |Kullanıcı adı için Windows kimlik doğrulaması. Kerberos kimlik doğrulaması için belirtin `<username>@<domain>.com`. |Evet (Windows kimlik doğrulaması için) |
| password |Windows kimlik doğrulaması için parola. |Evet (Windows kimlik doğrulaması için) |
| gatewayName |Data Factory hizmetinin HDFS'ye bağlanmak için kullanması gereken ağ geçidi adı. |Evet |
| encryptedCredential |[Yeni AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) erişim kimlik bilgisi çıktısı. |Hayır |

### <a name="using-anonymous-authentication"></a>Anonim kimlik doğrulaması

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Windows kimlik doğrulaması kullanma

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Veri kümesi özellikleri
Bölümleri ve veri kümeleri tanımlamak için kullanılabilir özellikleri tam listesi için bkz [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bölümler bir veri kümesi JSON İlkesi yapısı ve kullanılabilirlik gibi tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablo, vs.) için benzer.

**TypeProperties** bölümünde her veri kümesi türü için farklıdır ve verilerin veri deposundaki konumu hakkında bilgi sağlar. TypeProperties bölümü için veri kümesi türü **FileShare** (HDFS veri kümesini içeren) aşağıdaki özelliklere sahip

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasör yolu. Örnek: `myfolder`<br/><br/>Çıkış karakterini kullanma ' \ ' dizesinde özel karakterler için. Örneğin: folder\subfolder için klasörü belirtin\\\\alt d:\samplefolder için d: belirtin\\\\ÖrnekKlasör.<br/><br/>Bu özellik ile birleştirebilirsiniz **partitionBy** klasörün yol tabanlı slice başlangıç/bitiş tarih saatleri. |Evet |
| fileName |Dosya adı belirtin **folderPath** klasördeki belirli bir dosyaya başvurmak için tablo istiyorsanız. Bu özellik için herhangi bir değer belirtmezseniz, tabloda bir klasördeki tüm dosyaları işaret eder.<br/><br/>Oluşturulan dosyanın adını bir çıktı veri kümesi için dosya adı belirtilmediği durumlarda, aşağıdaki olacaktır bu biçimi: <br/><br/>`Data.<Guid>.txt` (örneğin:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Hayır |
| partitionedBy |partitionedBy dinamik bir folderPath, zaman serisi verileri için dosya adı belirtmek için kullanılabilir. Örnek: veri her saat için parametreli folderPath. |Hayır |
| format | Şu biçim türlerini destekler: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ayarlama **türü** özelliği şu değerlerden biri olarak biçimine altında. Daha fazla bilgi için [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [Json biçimine](data-factory-supported-file-and-compression-formats.md#json-format), [Avro biçimi](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format), ve [Parquetbiçimi](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümler. <br><br> İsterseniz **olarak dosya kopyalama-olan** dosya tabanlı depoları arasında (ikili kopya), her iki girdi ve çıktı veri kümesi tanımları biçimi bölümünde atlayın. |Hayır |
| compression | Veri sıkıştırma düzeyi ve türünü belirtin. Desteklenen türler şunlardır: **GZip**, **Deflate**, **Bzıp2**, ve **ZipDeflate**. Desteklenen düzeyleri şunlardır: **En iyi** ve **hızlı**. Daha fazla bilgi için [dosya ve sıkıştırma biçimleri Azure Data factory'de](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

> [!NOTE]
> Dosya adı ve fileFilter aynı anda kullanılamaz.

### <a name="using-partionedby-property"></a>PartionedBy özelliğini kullanma
Önceki bölümde belirtildiği gibi bir dinamik folderPath ve zaman serisi verileri ile dosya adını belirtebilirsiniz **partitionedBy** özelliği [Data Factory işlevleri ve sistem değişkenlerini](data-factory-functions-variables.md).

Zaman serisi veri kümeleri, zamanlama ve dilimleri hakkında daha fazla bilgi için bkz: [veri kümeleri oluşturma](data-factory-create-datasets.md), [zamanlama ve yürütme](data-factory-scheduling-and-execution.md), ve [işlem hatları oluşturma](data-factory-create-pipelines.md) makaleler.

#### <a name="sample-1"></a>Örnek 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Bu örnekte {dilim} belirtilen değeri (YYYYMMDDHH) biçiminde Data Factory sistem değişkeni SliceStart ile değiştirilir. Slicestart'taki saat diliminin başlangıç ifade eder. FolderPath için her bir dilimi farklıdır. Örneğin: wikidatagateway/wikisampledataout/2014100103 veya wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Örnek 2:

```JSON
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
Bu örnekte, folderPath ve dosya adı özellikleri tarafından kullanılan ayrı değişkenleri içine yıl, ay, gün ve saat SliceStart ayıklanır.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Bölümleri & etkinlikleri tanımlamak için mevcut özelliklerin tam listesi için bkz: [işlem hatları oluşturma](data-factory-create-pipelines.md) makalesi. Ad, açıklama, girdi ve çıktı tabloları ve ilkeleri gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Oysa etkinliğin typeProperties bölümündeki özellikler her etkinlik türü ile farklılık gösterir. Kopyalama etkinliği için kaynaklar ve havuzlar türlerine bağlı olarak farklılık gösterir.

Kopyalama etkinliği kaynak türü olduğunda, **FileSystemSource** typeProperties bölümünde aşağıdaki özellikler kullanılabilir:

**FileSystemSource** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| recursive |Belirtilen klasörün alt klasörleri ya da yalnızca veri yinelemeli olarak okunur olup olmadığını belirtir. |TRUE, False (varsayılan) |Hayır |

## <a name="supported-file-and-compression-formats"></a>Desteklenen dosya ve sıkıştırma biçimleri
Bkz: [dosya ve sıkıştırma biçimleri Azure Data factory'de](data-factory-supported-file-and-compression-formats.md) ilişkin ayrıntıları.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON örneği: Azure Blob için veri şirket içi hdfs kopyalama
Bu örnek, bir şirket içi HDFS Azure Blob depolama alanına veri kopyalama işlemi gösterilmektedir. Ancak, veriler kopyalanabilir **doğrudan** herhangi bir belirtilen havuzlarını [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopyalama etkinliğini kullanarak Azure Data Factory'de.  

Örnek, aşağıdaki Data Factory varlıkları için JSON tanımları sağlar. Verileri HDFS kullanarak Azure Blob depolama alanına kopyalamak için bir işlem hattı oluşturmak için bu tanımları kullanabilirsiniz [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Bağlı hizmet türü [OnPremisesHdfs](#linked-service-properties).
2. Bağlı hizmet türü [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [FileShare](#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) türü [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [işlem hattı](data-factory-create-pipelines.md) kullanan bir kopyalama etkinliği ile [FileSystemSource](#copy-activity-properties) ve [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Örnek verileri saatte bir Azure blob için bir şirket içi HDFS kopyalar. Bu örneklerde kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

İlk adım, veri yönetimi ağ geçidi ayarlama. Yönergeleri [Bulut ve şirket içi konumlar arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi.

**HDFS bağlı hizmeti:** Bu örnek, Windows kimlik doğrulaması kullanır. Bkz: [HDFS bağlı hizmet](#linked-service-properties) bölüm için farklı türde kimlik doğrulaması kullanabilirsiniz.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure depolama bağlı hizmeti:**

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

**HDFS girdi veri kümesi:** Bu veri kümesine başvuruyor HDFS klasöre DataTransfer/UnitTest /. İşlem hattı, bu klasördeki tüm dosyaları hedefe kopyalar.

"Dış" ayarını: "true" bildirir Data Factory hizmetinin veri kümesi dış veri fabrikasına ve veri fabrikasında bir etkinliği tarafından üretilen değil.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (Sıklık: saat, interval: 1). Blob için klasör yolu işlenmekte olan dilimin başlangıç zamanı temel alınarak dinamik olarak değerlendirilir. Yıl, ay, gün ve saat bölümlerini başlangıç zamanı klasör yolu kullanır.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Dosya sistemi kaynak ve havuz Blob ile bir işlem hattındaki kopyalama etkinliği:**

İşlem hattı, bu girdi ve çıktı veri kümelerini kullanmak için yapılandırıldığı ve saatte bir çalışacak şekilde zamanlanmış bir kopyalama etkinliği içeriyor. JSON tanımı, işlem hattındaki **kaynak** türü ayarlandığında **FileSystemSource** ve **havuz** türü ayarlandığında **BlobSink**. SQL sorgusu için belirtilen **sorgu** özelliği veri kopyalamak için son bir saat içinde seçer.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS Bağlayıcısı için Kerberos kimlik doğrulaması kullan
HDFS bağlayıcısında Kerberos kimlik doğrulamasını kullanmak için şirket içi ortamı ayarlamak için iki seçenek vardır. Bir Olayınıza daha iyi uyduğunu seçebilirsiniz.
* 1\. seçenek: [Ağ geçidi makinesi Kerberos bölgesinde katılın](#kerberos-join-realm)
* 2\. seçenek: [Windows etki alanı ve Kerberos alanı arasında karşılıklı güven etkinleştir](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>1. seçenek: Ağ geçidi makinesi Kerberos bölgesinde katılın

#### <a name="requirement"></a>Önkoşullar:

* Ağ geçidi makinesine Kerberos alanı katılmak gereken ve herhangi bir Windows etki alanına katılma işlemi gerçekleştirilemiyor.

#### <a name="how-to-configure"></a>Nasıl yapılandırılır:

**Ağ geçidi makinesinde:**

1.  Çalıştırma **Ksetup** Kerberos KDC sunucu ve bölgeyi yapılandırmak için yardımcı program.

    Kerberos alanı bir Windows etki alanından farklı olduğundan makine bir çalışma grubunun üyesi yapılandırılmalıdır. Bu, Kerberos alanı ayarlama ve şu şekilde bir KDC sunucu ekleyerek gerçekleştirilebilir. Değiştirin *REALM.COM* gerektiği gibi ilgili kendi bölge ile.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Yeniden** 2 Bu komutları yürütmeden sonra makine.

2.  Yapılandırmayı doğrulamak **Ksetup** komutu. Çıkış, gibi olmalıdır:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Azure Data Factory'de:**

* HDFS bağlayıcısını kullanarak yapılandırma **Windows kimlik doğrulaması** Kerberos asıl adı ve HDFS veri kaynağına bağlanmak için parola. Denetleme [HDFS bağlı hizmeti özellikleri](#linked-service-properties) yapılandırma ayrıntıları bölümü.

### <a name="kerberos-mutual-trust"></a>2. seçenek: Windows etki alanı ve Kerberos alanı arasında karşılıklı güven etkinleştir

#### <a name="requirement"></a>Önkoşullar:
*   Ağ geçidi makinesinde bir Windows etki alanına eklemeniz gerekir.
*   Etki alanı denetleyicisinin ayarlarını güncelleştirmek için izne ihtiyacınız var.

#### <a name="how-to-configure"></a>Nasıl yapılandırılır:

> [!NOTE]
> REALM.COM ve AD.COM aşağıdaki öğreticide kendi ilgili bölge ve etki alanı denetleyicisi gerektiği gibi değiştirin.

**KDC sunucusunda:**

1. KDC yapılandırmayı Düzenle **krb5.conf** KDC izin vermek için dosyanın Windows aşağıdaki yapılandırma şablonuna başvuran etki alanı güven. Varsayılan olarak, şu yapılandırma konumdadır **/etc/krb5.conf**.

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

   **Yeniden** yapılandırma sonrasında KDC Hizmeti.

2. Adlı bir asıl hazırlama **krbtgt/REALM.COM\@AD.COM** KDC sunucusunda aşağıdaki komutla:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. İçinde **hadoop.security.auth_to_local** HDFS hizmet yapılandırma dosyasında, ekleme `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Etki alanı denetleyicisinde:**

1.  Aşağıdaki komutu çalıştırın **Ksetup** komutları bölge giriş eklemek için:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Kerberos alanı için Windows etki alanı güven oluşturun. [parola] olan asıl parola **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Kerberos kullanılan şifreleme algoritması seçin.

    1. Sunucu Yöneticisi'ne gidin > Grup İlkesi Yönetimi > etki alanı > Grup İlkesi nesneleri > Varsayılan veya etkin bir etki alanı ilkesi ve düzenleyin.

    2. İçinde **Grup İlkesi Yönetimi Düzenleyicisi** açılan pencere, bilgisayar yapılandırması gidin > ilkeler > Windows Ayarları > Güvenlik Ayarları > yerel ilkeler > güvenlik seçeneklerini ve yapılandırma **ağ Güvenlik: Kerberos'ta izin verilen şifreleme türlerini yapılandır**.

    3. Seçin, kullanmak istediğiniz şifreleme algoritması için KDC bağlanın. Genellikle, yalnızca tüm seçenekleri seçebilirsiniz.

        ![Kerberos için şifreleme türlerini yapılandırma](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Kullanım **Ksetup** belirli bölge üzerinde kullanılacak şifreleme algoritmasını belirtmek için komutu.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Windows etki alanında Kerberos asıl kullanmak için etki alanı hesabını ve Kerberos asıl arasındaki eşleme oluşturun.

    1. Yönetimsel Araçları başlatmak > **Active Directory Kullanıcıları ve Bilgisayarları**.

    2. Tıklayarak Gelişmiş özellikleri yapılandırma **görünümü** > **Gelişmiş Özellikler**.

    3. Hangi eşlemeleri oluşturmak istiyorsanız ve görüntülemek için sağ hesabını bulun **adı eşlemeleri** > tıklatın **Kerberos adları** sekmesi.

    4. Sorumlu bölge ekleyin.

        ![Güvenlik kimliğine eşleyin](media/data-factory-hdfs-connector/map-security-identity.png)

**Ağ geçidi makinesinde:**

* Aşağıdaki komutu çalıştırın **Ksetup** bölge giriş eklemek için komutları.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory'de:**

* HDFS bağlayıcısını kullanarak yapılandırma **Windows kimlik doğrulaması** etki alanı hesabı veya Kerberos asıl HDFS veri kaynağına bağlanmak için birlikte. Denetleme [HDFS bağlı hizmeti özellikleri](#linked-service-properties) yapılandırma ayrıntıları bölümü.

> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesi sütunlara eşlemek için bkz: [Azure Data factory'de veri kümesi sütunlarını eşleme](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Performans ve ayarlama
Bkz: [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md) veri taşıma (kopyalama etkinliği) Azure Data Factory ve bunu en iyi duruma getirmek için çeşitli yollar, performansı etkileyebilir anahtar Etkenler hakkında bilgi edinmek için.
