---
title: Azure Data Factory kullanarak SAP HANA verileri taşıma
description: Azure Data Factory kullanarak SAP HANA verileri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ebd1cf22bffc6a136845672cedcefa7936eeece5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682365"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP HANA verileri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-sap-hana-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-sap-hana.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de SAP HANA Bağlayıcısı](../connector-sap-business-warehouse.md)' na bakın.

Bu makalede, verileri şirket içi SAP HANA taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Şirket içi SAP HANA veri deposundaki verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca bir SAP HANA diğer veri depolarına veri taşımayı destekler, ancak diğer veri depolarından verileri bir SAP HANA taşımaya yönelik değildir.

## <a name="supported-versions-and-installation"></a>Desteklenen sürümler ve yükleme
Bu bağlayıcı SAP HANA veritabanının herhangi bir sürümünü destekler. SQL sorguları kullanılarak HANA bilgi modellerinden (analitik ve hesaplama görünümleri gibi) ve satır/sütun tablolarının verilerinin kopyalanmasını destekler.

SAP HANA örneğine bağlantıyı etkinleştirmek için aşağıdaki bileşenleri yüklemelisiniz:
- **Veri yönetimi Gateway**: Data Factory hizmeti, veri yönetimi Gateway adlı bir bileşeni kullanarak şirket içi veri depolarına (SAP HANA dahil) bağlanmayı destekler. Ağ geçidini ayarlamaya yönelik Veri Yönetimi ağ geçidi ve adım adım yönergeler hakkında bilgi edinmek için bkz. [Şirket içi veri deposu ile bulut veri deposu arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi. SAP HANA bir Azure IaaS sanal makinesinde (VM) barındırıldığından bile ağ geçidi gerekir. Ağ geçidini, veri deposuyla aynı VM 'ye veya ağ geçidinin veritabanına bağlanabildiği sürece farklı bir VM 'ye yükleyebilirsiniz.
- Ağ Geçidi makinesinde **ODBC sürücüsü SAP HANA** . SAP HANA ODBC sürücüsünü [SAP Software Download Center](https://support.sap.com/swdc)'dan indirebilirsiniz. **Windows için SAP HANA**anahtar sözcüğünü kullanarak arama yapın. 

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak verileri şirket içi SAP HANA veri deposundan taşıyan kopyalama etkinliğiyle bir işlem hattı oluşturabilirsiniz. 

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) . 
- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. 
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. 

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Şirket içi SAP HANA veri kopyalamak için kullanılan Data Factory varlıkların JSON tanımlarına sahip bir örnek için, bu makalenin [JSON örneği: SAP HANA verileri Azure Blob 'A kopyalama](#json-example-copy-data-from-sap-hana-to-azure-blob) bölümüne bakın. 

Aşağıdaki bölümler, bir SAP HANA veri deposuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda SAP HANA bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
sunucu | SAP HANA örneğinin bulunduğu sunucunun adı. Sunucunuz özelleştirilmiş bir bağlantı noktası kullanıyorsa `server:port` belirtin. | string | Evet
authenticationType | Kimlik doğrulama türü. | dizisinde. "Temel" veya "Windows" | Evet 
kullanıcı adı | SAP sunucusuna erişimi olan kullanıcının adı | string | Evet
password | Kullanıcının parolası. | string | Evet
gatewayName | Data Factory hizmetinin şirket içi SAP HANA örneğine bağlanmak için kullanması gereken ağ geçidinin adı. | string | Evet
encryptedCredential | Şifrelenmiş kimlik bilgisi dizesi. | string | Hayır

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Relationaltable**türünde SAP HANA veri kümesi için desteklenen türe özgü özellik yok. 


## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları gibi özellikler, tüm etkinlik türleri için kullanılabilir ilkeleridir.

Ancak, etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Copy etkinliğinin kaynağı **Relationalsource** (SAP HANA içerir) türünde olduğunda, typeproperties bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu | SAP HANA örneğinden verileri okumak için SQL sorgusunu belirtir. | SQL sorgusu. | Evet |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON örneği: SAP HANA verileri Azure Blob 'a kopyalama
Aşağıdaki örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Bu örnek, şirket içi SAP HANA verilerinin bir Azure Blob depolama alanına nasıl kopyalanacağını gösterir. Bununla birlikte, veriler, Azure Data Factory kopyalama etkinliği kullanılarak [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) listelenen herhangi bir havuza **doğrudan** kopyalanabilir.  

> [!IMPORTANT]
> Bu örnek, JSON parçacıkları sağlar. Veri Fabrikası oluşturmaya yönelik adım adım yönergeler içermez. Adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makalesi arasında verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

1. [Saphana](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Relationaltable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Relationalsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri bir SAP HANA örneğinden Azure blobuna saatlik olarak kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini kurun. Yönergeler, [Şirket içi konumlar ve bulut makaleleri arasında hareketli verilerde](data-factory-move-data-between-onprem-and-cloud.md) yer alır.

### <a name="sap-hana-linked-service"></a>Bağlı hizmet SAP HANA
Bu bağlı hizmet, SAP HANA örneğinizi veri fabrikasına bağlar. Type özelliği **Saphana**olarak ayarlanır. TypeProperties bölümü SAP HANA örneğine yönelik bağlantı bilgilerini sağlar.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti
Bu bağlı hizmet, Azure depolama hesabınızı veri fabrikasına bağlar. Type özelliği **Azurestorage**olarak ayarlanır. TypeProperties bölümü, Azure depolama hesabı için bağlantı bilgilerini sağlar.

```json
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

### <a name="sap-hana-input-dataset"></a>SAP HANA girişi veri kümesi

Bu veri kümesi SAP HANA veri kümesini tanımlar. Data Factory veri kümesinin türünü **Relationaltable**olarak ayarlarsınız. Şu anda, bir SAP HANA veri kümesi için herhangi bir türe özgü özellik belirtmeyin. Kopyalama etkinliği tanımındaki sorgu SAP HANA örneğinden hangi verilerin okunacağını belirtir. 

External özelliğinin true olarak ayarlanması, Data Factory hizmetine tablonun veri fabrikasının dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir.

Sıklık ve Aralık özellikleri zamanlamayı tanımlar. Bu durumda, veriler SAP HANA örneğinden her saat okunmalıdır. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob çıktı veri kümesi
Bu veri kümesi, çıkış Azure blob veri kümesini tanımlar. Type özelliği AzureBlob olarak ayarlanır. TypeProperties bölümü, SAP HANA örneğinden kopyalanmış verilerin nerede depolandığını sağlar. Veriler her saat yeni bir bloba yazılır (sıklık: Hour, Interval: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Kopyalama etkinliği içeren işlem hattı

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü, **relationalsource** (SAP HANA kaynağı için) olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>SAP HANA için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi, kopyalama etkinliği, kaynak türlerindeki otomatik tür dönüştürmeleri aşağıdaki iki adımlı yaklaşımla birlikte havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

SAP HANA verileri taşırken, SAP HANA türlerinden .NET türlerine aşağıdaki eşlemeler kullanılır.

SAP HANA türü | .NET tabanlı tür
------------- | ---------------
Iç | Bayt
Small | Int16
INT | Int32
BIGıNT | Int64
GERÇEK | Tek
ÇIFT | Tek
DECIMAL | Kategori
BOOLEAN | Bayt
VARCHAR | Dize
NVARCHAR | Dize
CLOB | Byte []
ALHAYALI um | Dize
BLOB | Byte []
DATE | DateTime
TIME | TimeSpan
ILIŞKIN | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Bilinen sınırlamalar
SAP HANA verileri kopyalarken bazı bilinen sınırlamalar vardır:

- NVARCHAR dizeleri 4000 Unicode karakterlik maksimum uzunluğa kesilir
- SMALLDECIMAL desteklenmiyor
- VARBINARY desteklenmiyor
- Geçerli tarihler 1899/12/30 ile 9999/12/31 arasındadır

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
