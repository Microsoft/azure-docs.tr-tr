---
title: Şirket içinden veri taşıma
description: Azure Data Factory kullanarak verileri Şirket içinden nasıl taşıyacağınız hakkında bilgi edinin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 52e176e0fed85b649d482614667d695db539e5d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383084"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Azure Data Factory kullanarak verileri Şirket içinden taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-hdfs-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-hdfs.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de](../connector-hdfs.md), bkz..

Bu makalede, verileri Şirket içinden bir sunucudan taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Her türlü desteklenen havuz veri deposuna verileri bir sunucudan kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca şirket içi bir sunucudan diğer veri depolarına veri taşımayı destekler, ancak verileri diğer veri depolarından şirket içi bir e-posta ile taşımak için kullanmaz.

> [!NOTE]
> Kopyalama etkinliği, kaynak dosyayı başarıyla hedefe kopyalandıktan sonra silmez. Başarılı bir kopyadan sonra kaynak dosyayı silmeniz gerekiyorsa, dosyayı silmek ve işlem hattındaki etkinliği kullanmak için özel bir etkinlik oluşturun. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Bağlantı etkinleştiriliyor
Data Factory hizmeti, Veri Yönetimi ağ geçidini kullanarak şirket içi bir sunucuya bağlanmayı destekler. Veri Yönetimi ağ geçidini ayarlama hakkında bilgi edinmek ve ağ geçidini ayarlamaya yönelik adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makaleleri arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) . Azure IaaS VM 'de barındırıldığında, bu ağ geçidini kullanarak bağlantı kurmak için ağ geçidini kullanın.

> [!NOTE]
> Veri Yönetimi ağ geçidinin **Tüm** [name node Server]: [ad node port] ve [veri düğüm sunucuları]: [veri düğümü bağlantı noktası] için Hadoop kümesinin tamamına erişebildiğinizden emin olun. Varsayılan [ad düğümü bağlantı noktası] 50070, varsayılan [veri düğümü bağlantı noktası] 50075 ' dir.

Ağ geçidini, aynı şirket içi makineye veya Azure VM 'ye bir bilgisayar adına yükleyebilir, ağ geçidini ayrı bir makineye/Azure IaaS VM 'sine yüklemenizi öneririz. Ağ geçidinin ayrı bir makinede bulunması, kaynak çekişmesini azaltır ve performansı geliştirir. Ağ geçidini ayrı bir makineye yüklediğinizde, makinenin makineye bilgisayar adına erişebilmesi gerekir.

## <a name="getting-started"></a>Kullanmaya başlama
Farklı araçları/API 'Leri kullanarak bir, bir, bir

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API** ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Bir bir bir bir bir, bir bir bir bir bir. veri deposundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarına sahip bir örnek için, bkz. [JSON örneği: Bu makaledeki verileri şirket Içinden Azure Blob 'A kopyalama](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) .

Aşağıdaki bölümlerde, Data Factory 'e özgü varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılar sağlanmaktadır:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Bağlı hizmet bir veri deposunu veri fabrikasına bağlar. Bir şirket içi bir e-posta **ile veri** fabrikanıza bağlamak için, bir bağlantı türü olarak bir bağlı hizmet oluşturursunuz. Aşağıdaki tabloda,,,,

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tür |Type **özelliği:,** olarak ayarlanmalıdır |Yes |
| url |Bu URL 'nin URL 'si |Yes |
| authenticationType |Anonim veya Windows. <br><br> Bir bağlantı **kimliği Için Kerberos kimlik doğrulaması** kullanmak üzere, şirket içi ortamınızı uygun şekilde ayarlamak için [Bu bölüme](#use-kerberos-authentication-for-hdfs-connector) bakın. |Yes |
| userName |Windows kimlik doğrulaması için Kullanıcı adı. Kerberos kimlik doğrulaması için, belirtin `<username>@<domain>.com` . |Evet (Windows kimlik doğrulaması için) |
| password |Windows kimlik doğrulaması için parola. |Evet (Windows kimlik doğrulaması için) |
| gatewayName |Data Factory hizmetinin, bir hizmetin bir bağlantı kurmak için kullanması gereken ağ geçidinin adı. |Yes |
| encryptedCredential |Erişim kimlik bilgisinin [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) çıkışı. |No |

### <a name="using-anonymous-authentication"></a>Anonim kimlik doğrulaması kullanma

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

### <a name="using-windows-authentication"></a>Windows kimlik doğrulamasını kullanma

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
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. Dosya kümesinin dosya kümesi için typeProperties bölümü **(** IBir IBU veri kümesi de içerir) aşağıdaki özelliklere sahiptir

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |Klasörün yolu. Örnek: `myfolder`<br/><br/>Dizedeki özel karakterler için ' \ ' kaçış karakterini kullanın. Örneğin: folder\alt klasörü için klasör alt klasörü \\ \\ ve d:\samplefolder için d: \\ \\ samplefolder belirtin.<br/><br/>Bu özelliği, dilim başlangıç/bitiş tarihi-saati temelinde klasör yolları sağlamak için **Partitionby** ile birleştirebilirsiniz. |Yes |
| fileName |Tablonun klasördeki belirli bir dosyaya başvurmasını istiyorsanız, **FolderPath** içindeki dosyanın adını belirtin. Bu özellik için herhangi bir değer belirtmezseniz tablo, klasördeki tüm dosyaları gösterir.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde, oluşturulan dosyanın adı şu biçimde olacaktır: <br/><br/>`Data.<Guid>.txt` (örneğin:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy, zaman serisi verilerine yönelik bir dinamik folderPath, filename belirtmek için kullanılabilir. Örnek: her saat veri için folderPath parametreli parametrelenir. |No |
| biçim | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |No |
| sıkıştırma | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2** ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> filename ve fileFilter aynı anda kullanılamaz.

### <a name="using-partionedby-property"></a>PartionedBy özelliği kullanma
Önceki bölümde belirtildiği gibi, **Partitionedby** özelliği, [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md)ile zaman serisi verileri için dinamik bir FolderPath ve filename belirtebilirsiniz.

Zaman serisi veri kümeleri, zamanlama ve dilimler hakkında daha fazla bilgi edinmek için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md), [& yürütmeyi zamanlama](data-factory-scheduling-and-execution.md)ve işlem [hattı makaleleri oluşturma](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>Örnek 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Bu örnekte, {Slice}, belirtilen biçimde (YYYYMMDDHH) Data Factory sistem değişkeni değeri ile değiştirilmiştir. Dilimcstart, dilimin başlangıç zamanına başvurur. FolderPath her bir dilim için farklıdır. Örneğin: wikidatagateway/wisvahili amptadataout/2014100103 veya wikidatagateway/wisvahili amptadataout/2014100104.

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
Bu örnekte, monthestart 'ın Year, month, Day ve Time, folderPath ve fileName özellikleri tarafından kullanılan ayrı değişkenlere ayıklanır.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilkeler gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Kopyalama etkinliği için kaynak **Filesystemsource** türünde olduğunda, typeproperties bölümünde aşağıdaki özellikler mevcuttur:

**Filesystemsource** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True, false (varsayılan) |No |

## <a name="supported-file-and-compression-formats"></a>Desteklenen dosya ve sıkıştırma biçimleri
Ayrıntılar hakkında [Azure Data Factory makalesinde dosya ve sıkıştırma biçimlerine](data-factory-supported-file-and-compression-formats.md) bakın.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON örneği: şirket içi sunucudan Azure Blob 'a veri kopyalama
Bu örnek, şirket içi bir sunucudan Azure Blob depolama alanına nasıl veri kopyalanacağını gösterir. Ancak, veriler, burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza **doğrudan** kopyalanabilir.  

Örnek, aşağıdaki Data Factory varlıkları için JSON tanımları sağlar. Bu tanımları, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak Azure Blob depolama 'dan veri kopyalamak üzere bir işlem hattı oluşturmak için kullanabilirsiniz.

1. [OnPremisesHdfs](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [FileShare](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Filesystemsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri her saat için bir şirket içi bir Azure blobuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini ayarlayın. Şirket [içi konumlar ve bulut makalesi arasında taşınan verilerdeki](data-factory-move-data-between-onprem-and-cloud.md) yönergeler.

Bu **bağlı hizmet:** Bu örnek, Windows kimlik doğrulamasını kullanır. Kullanabileceğiniz farklı kimlik doğrulama türleri [için bkz.](#linked-service-properties) .

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

Bir **giriş veri kümesi:** Bu veri kümesi, Sımfolder DataTransfer/UnitTest/değerini ifade eder. İşlem hattı, bu klasördeki tüm dosyaları hedefe kopyalar.

"External": "true" ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

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

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

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

**Dosya sistemi kaynağına ve BLOB havuzuna sahip bir işlem hattındaki kopyalama etkinliği:**

İşlem hattı, bu giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü, **filesystemsource** olarak ayarlanır ve **Havuz** türü **blobsink** olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

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

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Bağlantı kimliği için Kerberos kimlik doğrulaması kullan
Bir şirket içi ortamı ayarlamak için, bu şekilde iki seçenek bulunur. Büyük/küçük harf durumuna göre daha iyi bir seçim yapabilirsiniz.
* Seçenek 1: [ağ geçidi makinesini Kerberos bölgesi 'Nde birleştirin](#kerberos-join-realm)
* 2. seçenek: [Windows etki alanı ve Kerberos bölgesi arasında karşılıklı güveni etkinleştirin](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Seçenek 1: ağ geçidi makinesini Kerberos bölgesi 'nde birleştirin

#### <a name="requirement"></a>Malzeme

* Ağ Geçidi makinesinin Kerberos bölgesine katılması ve herhangi bir Windows etki alanına katılamıyor olması gerekir.

#### <a name="how-to-configure"></a>Nasıl yapılandırılır:

**Ağ Geçidi makinesinde:**

1. Kerberos KDC sunucusunu ve bölgesini yapılandırmak için **Ksetup** yardımcı programını çalıştırın.

   Kerberos bölgesi bir Windows etki alanından farklı olduğundan, makinenin bir çalışma grubunun üyesi olarak yapılandırılması gerekir. Bu, Kerberos bölgesi ayarlanarak ve bir KDC sunucusunu aşağıdaki gibi ekleyerek elde edilebilir. *Realm.com* değerini, gereken şekilde kendi ilgili bölge ile değiştirin.

   ```cmd
   Ksetup /setdomain REALM.COM
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ```

   Bu 2 komutu yürüttükten sonra makineyi **yeniden başlatın** .

2. **Ksetup** komutuyla yapılandırmayı doğrulayın. Çıktının şöyle olması gerekir:

   ```cmd
   Ksetup
   default realm = REALM.COM (external)
   REALM.com:
      kdc = <your_kdc_server_address>
   ```

**Azure Data Factory:**

* Bir **Windows kimlik doğrulamasını** kullanarak, bu TBU veri kaynağına bağlanmak Için, Kerberos asıl adınız ve parolanızla birlikte, Yapılandırma ayrıntıları sayfasında, bu [bağlantı hizmeti özellikleri](#linked-service-properties) bölümüne bakın.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>2. seçenek: Windows etki alanı ve Kerberos bölgesi arasında karşılıklı güveni etkinleştirin

#### <a name="requirement"></a>Malzeme
*    Ağ Geçidi makinesi bir Windows etki alanına katılması gerekir.
*    Etki alanı denetleyicisinin ayarlarını güncelleştirmek için izninizin olması gerekir.

#### <a name="how-to-configure"></a>Nasıl yapılandırılır:

> [!NOTE]
> Aşağıdaki öğreticide, REALM.COM ve AD.COM ' i kendi ilgili bölge ve etki alanı denetleyiciyle gerektiği şekilde değiştirin.

**KDC sunucusunda:**

1. KDC güveni Windows etki alanının aşağıdaki yapılandırma şablonuna başvurmasına izin vermek için **krb5. conf** dosyasındaki KDC yapılandırmasını düzenleyin. Varsayılan olarak, yapılandırma **/etc/kronb5,conf** konumunda bulunur.

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

   Yapılandırmadan sonra KDC hizmetini **yeniden başlatın** .

2. Aşağıdaki komutla, KDC sunucusunda **krbtgt/Realm. COM \@ ad.com** adlı bir sorumlu hazırlayın:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. **Hadoop.Security.auth_to_local** , hizmet yapılandırma dosyasında, ekleyin `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Etki alanı denetleyicisinde:**

1. Bir bölge girişi eklemek için aşağıdaki **Ksetup** komutlarını çalıştırın:

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

2. Windows etki alanından Kerberos bölgesine güven oluşturun. [password] sorumlu **krbtgt/Realm. COM \@ ad.com** için paroladır.

   ```cmd
   netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
   ```

3. Kerberos 'da kullanılan şifreleme algoritmasını seçin.

   1. Sunucu Yöneticisi > grup ilkesi yönetim > etki alanı > Grup İlkesi nesneleri > varsayılan veya etkin etki alanı Ilkesi ' ne gidin ve düzenleyin.

   2. **Grup İlkesi Yönetimi Düzenleyicisi** açılır penceresinde, bilgisayar yapılandırması > Ilkeler > Windows ayarları > güvenlik ayarları > yerel Ilkeler > güvenlik seçenekleri ' ne gidin ve **ağ güvenliğini yapılandırın: Kerberos Için izin verilen şifreleme türlerini yapılandırın**.

   3. KDC 'ye bağlanırken kullanmak istediğiniz şifreleme algoritmasını seçin. Genellikle, tüm seçenekleri seçmeniz yeterlidir.

      ![Kerberos için yapılandırma şifreleme türleri](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

   4. Belirli bir bölgede kullanılacak şifreleme algoritmasını belirtmek için **Ksetup** komutunu kullanın.

      ```cmd
      ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
      ```

4. Windows etki alanında Kerberos sorumlusu kullanmak için etki alanı hesabı ve Kerberos sorumlusu arasındaki eşlemeyi oluşturun.

   1. **Kullanıcı ve bilgisayar Active Directory** yönetim araçlarını > başlatın.

   2. Gelişmiş özellikleri **görüntüle** gelişmiş özellikleri ' ne tıklayarak yapılandırın  >  .

   3. Eşleme oluşturmak istediğiniz hesabı bulun ve **ad eşlemelerini** görüntülemek için sağ tıklayın > **Kerberos adları** sekmesi ' ne tıklayın.

   4. Realm 'tan bir sorumlu ekleyin.

      ![Eşleme güvenlik kimliği](media/data-factory-hdfs-connector/map-security-identity.png)

**Ağ Geçidi makinesinde:**

* Bir bölge girişi eklemek için aşağıdaki **Ksetup** komutlarını çalıştırın.

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Azure Data Factory:**

* **Windows kimlik doğrulamasını** kullanarak, TBU veri kaynağına bağlanmak Için etki alanı hesabınızla veya Kerberos sorumlusuyla birlikte,, Yapılandırma ayrıntıları sayfasında, bu [bağlantı hizmeti özellikleri](#linked-service-properties) bölümüne bakın.

> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
