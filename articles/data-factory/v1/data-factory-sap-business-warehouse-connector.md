---
title: Azure Data Factory kullanarak SAP Business Warehouse 'tan veri taşıma
description: Azure Data Factory kullanarak SAP Business Warehouse 'tan veri taşıma hakkında bilgi edinin.
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
ms.openlocfilehash: 47bc2db8730ebdedd180646d2fb86b642bbc631d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666034"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP Business Warehouse 'Tan veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-sap-business-warehouse-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de SAP Business Warehouse Connector](../connector-sap-business-warehouse.md).


Bu makalede, verileri şirket içi SAP Business Warehouse 'tan (siyah beyaz) taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Şirket içi bir SAP Business Warehouse veri deposundaki verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca bir SAP Iş ambarından diğer veri depolarına veri taşımayı destekler, ancak verileri diğer veri depolarından SAP Iş ambarına taşımak için kullanmaz. 

## <a name="supported-versions-and-installation"></a>Desteklenen sürümler ve yükleme
Bu bağlayıcı SAP Business Warehouse sürüm 7. x ' i destekler. MDX sorgularını kullanarak InfoCubes ve Queryküplerinden (BEx sorguları dahil) verilerin kopyalanmasını destekler.

SAP BW örneğine bağlantıyı etkinleştirmek için aşağıdaki bileşenleri yüklemelisiniz:
- **Veri yönetimi Gateway**: Data Factory hizmeti, veri yönetimi Gateway adlı bir bileşeni kullanarak şirket içi veri DEPOLARıNA (SAP Business Warehouse dahil) bağlanmayı destekler. Ağ geçidini ayarlamaya yönelik Veri Yönetimi ağ geçidi ve adım adım yönergeler hakkında bilgi edinmek için bkz. [Şirket içi veri deposu ile bulut veri deposu arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi. SAP Business Warehouse bir Azure IaaS sanal makinesinde (VM) barındırıldığından bile ağ geçidi gereklidir. Ağ geçidini, veri deposuyla aynı VM 'ye veya ağ geçidinin veritabanına bağlanabildiği sürece farklı bir VM 'ye yükleyebilirsiniz.
- Ağ Geçidi makinesinde **SAP NetWeaver kitaplığı** . SAP NetWeaver kitaplığını SAP yöneticinizden alabilir veya doğrudan [SAP Software Download Center](https://support.sap.com/swdc)' dan edinebilirsiniz. En son sürüme ait indirme konumunu almak için **SAP Note #1025361** aratın. SAP NetWeaver kitaplığı (32-bit veya 64-bit) mimarisinin ağ geçidi yüklemenize uyduğundan emin olun. Daha sonra SAP NetWeaver RFC SDK 'sına dahil edilen tüm dosyaları SAP notuna göre yükler. SAP NetWeaver kitaplığı, SAP Istemci araçları yüklemesine de dahildir.

> [!TIP]
> NetWeaver RFC SDK 'dan ayıklanan dll 'leri System32 klasörüne koyun.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak şirket içi Cassandra veri deposundan veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz. 

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) . 
- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. 
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. 

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Şirket içi SAP Business Warehouse 'tan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarına sahip bir örnek için, bu makalenin [JSON örneği: SAP Business Warehouse 'Dan Azure Blob 'a veri kopyalama](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) bölümüne bakın. 

Aşağıdaki bölümler, bir SAP BW veri deposuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda SAP Business Warehouse (siyah-beyaz) bağlantılı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
sunucu | SAP BW örneğinin bulunduğu sunucunun adı. | string | Evet
systemNumber | SAP BW sisteminin sistem numarası. | Dize olarak temsil edilen iki basamaklı ondalık sayı. | Evet
ClientID | SAP W sistemindeki istemcinin istemci KIMLIĞI. | Dize olarak temsil edilen üç basamaklı ondalık sayı. | Evet
kullanıcı adı | SAP sunucusuna erişimi olan kullanıcının adı | string | Evet
password | Kullanıcının parolası. | string | Evet
gatewayName | Data Factory hizmetinin şirket içi SAP BW örneğine bağlanmak için kullanması gereken ağ geçidinin adı. | string | Evet
encryptedCredential | Şifrelenmiş kimlik bilgisi dizesi. | string | Hayır

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Relationaltable**türünde SAP BW veri kümesi için desteklenen türe özgü özellik yok. 


## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları gibi özellikler, tüm etkinlik türleri için kullanılabilir ilkeleridir.

Ancak, etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Copy etkinliğinin kaynağı **Relationalsource** (SAP BW içerir) türünde olduğunda, typeproperties bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu | SAP BW örneğinden verileri okumak için MDX sorgusunu belirtir. | MDX sorgusu. | Evet |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON örneği: SAP Business Warehouse 'tan Azure Blob 'a veri kopyalama
Aşağıdaki örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Bu örnek, şirket içi SAP Business Warehouse 'tan bir Azure Blob depolama alanına nasıl veri kopyalanacağını gösterir. Ancak, veriler, burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza **doğrudan** kopyalanabilir.  

> [!IMPORTANT]
> Bu örnek, JSON parçacıkları sağlar. Veri Fabrikası oluşturmaya yönelik adım adım yönergeler içermez. Adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makalesi arasında verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

1. [Sapbeyaz](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Relationaltable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Relationalsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri bir SAP Business Warehouse örneğinden saatlik olarak bir Azure blobuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini kurun. Yönergeler, [Şirket içi konumlar ve bulut makaleleri arasında hareketli verilerde](data-factory-move-data-between-onprem-and-cloud.md) yer alır.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse bağlı hizmeti
Bu bağlı hizmet, SAP BW örneğinizi veri fabrikasına bağlar. Type özelliği **Sapbeyaz**olarak ayarlanır. TypeProperties bölümü SAP BW örneğine yönelik bağlantı bilgilerini sağlar. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>SAP BW girişi veri kümesi
Bu veri kümesi SAP Business Warehouse veri kümesini tanımlar. Data Factory veri kümesinin türünü **Relationaltable**olarak ayarlarsınız. Şu anda, bir SAP BW veri kümesi için herhangi bir türe özgü özellik belirtmeyin. Kopyalama etkinliği tanımındaki sorgu SAP BW örneğinden hangi verilerin okunacağını belirtir. 

External özelliğinin true olarak ayarlanması, Data Factory hizmetine tablonun veri fabrikasının dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir.

Sıklık ve Aralık özellikleri zamanlamayı tanımlar. Bu durumda, veriler SAP BW örneğinden her saat okunmalıdır. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Bu veri kümesi, çıkış Azure blob veri kümesini tanımlar. Type özelliği AzureBlob olarak ayarlanır. TypeProperties bölümü, SAP BW örneğinden kopyalanmış verilerin nerede depolandığını sağlar. Veriler her saat yeni bir bloba yazılır (sıklık: Hour, Interval: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü, **relationalsource** (SAP BW kaynağı için) olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sorgu** özelliği için belirtilen sorgu, kopyalamanın Son saatteki verilerini seçer.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>SAP BW için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi, kopyalama etkinliği, kaynak türlerindeki otomatik tür dönüştürmeleri aşağıdaki iki adımlı yaklaşımla birlikte havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

SAP BW verileri taşırken, SAP BW türlerinden .NET türlerine aşağıdaki eşlemeler kullanılır.

ABAP Dictionary içindeki veri türü | .NET veri türü
-------------------------------- | --------------
ACCP |  'Tir
CHAR | Dize
CLNT | Dize
CURR | Kategori
CUKY | Dize
DEC | Kategori
FLTP | Çift
INT1 | Bayt
INT2 | Int16
INT4 | 'Tir
LANG | Dize
LCHR | Dize
LRAW | Byte []
PREC | Int16
QUA | Kategori
Madde | Byte []
RAWSTRING | Byte []
DIZISINDE | Dize
BİRİM | Dize
KATALAR | Dize
NUMC | Dize
TIMS | Dize

> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
