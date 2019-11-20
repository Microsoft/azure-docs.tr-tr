---
title: Azure Data Factory kullanarak Sybase 'ten veri taşıma
description: Azure Data Factory kullanarak Sybase veritabanından veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e6d2acbb848a03d950e830458963ecc4f26dff98
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682402"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Azure Data Factory kullanarak Sybase 'ten veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-onprem-sybase-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-sybase.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de Sybase Bağlayıcısı](../connector-sybase.md)' na bakın.

Bu makalede, verileri şirket içi bir Sybase veritabanından taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Şirket içi bir Sybase veri deposundan, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca bir Sybase veri deposundan diğer veri depolarına veri taşımayı destekler, ancak diğer veri depolarından verileri bir Sybase veri deposuna taşımamaktadır. 

## <a name="prerequisites"></a>Ön koşullar
Data Factory hizmeti, Veri Yönetimi ağ geçidini kullanarak şirket içi Sybase kaynaklarına bağlanmayı destekler. Veri Yönetimi ağ geçidini ayarlama hakkında bilgi edinmek ve ağ geçidini ayarlamaya yönelik adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makaleleri arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

Sybase veritabanı bir Azure IaaS VM 'sinde barındırıldığında bile ağ geçidi gereklidir. Ağ geçidini, veri deposuyla aynı IaaS sanal makinesine veya ağ geçidinin veritabanına bağlanabildiği sürece farklı bir VM 'ye yükleyebilirsiniz.

> [!NOTE]
> Bağlantı/ağ geçidi ile ilgili sorunları gidermeye yönelik ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Desteklenen sürümler ve yükleme
Sybase veritabanına bağlanmak üzere Veri Yönetimi ağ geçidinin, Veri Yönetimi ağ geçidi ile aynı sisteme [Sybase ıheryer. Data. SQLAnywhere 16 veya üzeri için veri sağlayıcısını](https://go.microsoft.com/fwlink/?linkid=324846) yüklemeniz gerekir. 

SAP Sybase SQL her yerde (ASA) 16 ve üzeri sürüm desteklenir; IQ ve ATıCı desteklenmez.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak şirket içi Cassandra veri deposundan veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz. 

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) . 
- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. 
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. 

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Şirket içi bir Sybase veri deposundan veri kopyalamak için kullanılan Data Factory varlıkların JSON tanımlarına sahip bir örnek için, bkz. [JSON örneği: Bu makalenin verileri Sybase 'Den Azure Blob 'A kopyalama](#json-example-copy-data-from-sybase-to-azure-blob) bölümü. 

Aşağıdaki bölümler, bir Sybase veri deposuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, Sybase bağlantılı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **OnPremisesSybase** olarak ayarlanmalıdır |Evet |
| sunucu |Sybase sunucusunun adı. |Evet |
| veritabanı |Sybase veritabanının adı. |Evet |
| manızı |Veritabanındaki şemanın adı. |Hayır |
| authenticationType |Sybase veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim, temel ve Windows. |Evet |
| kullanıcı adı |Temel veya Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |
| gatewayName |Data Factory hizmetinin şirket içi Sybase veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Relationaltable** türündeki veri kümesinin **typeproperties** bölümü (Sybase veri kümesini içerir) aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |İlişkili hizmetin başvurduğu Sybase veritabanı örneğindeki tablonun adı. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik & özelliklerinin tam listesi için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesi. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Kaynak, **Relationalsource** türünde olduğunda (Sybase içeren), **typeproperties** bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: select * from MyTable. |Hayır ( **veri kümesi** **TableName** belirtilmişse) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>JSON örneği: Sybase 'ten Azure Blob 'a veri kopyalama
Aşağıdaki örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Bu kişiler, verileri Sybase veritabanından Azure Blob depolama alanına kopyalamayı gösterir. Ancak, veriler burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza kopyalanabilir.   

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

1. [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde beğenilen bir hizmet.
3. [Relationaltable](data-factory-onprem-sybase-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Relationalsource](data-factory-onprem-sybase-connector.md#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip işlem [hattı](data-factory-create-pipelines.md) .

Örnek, bir sorgu sonucundan verileri her saat bir bloba blob 'a kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini kurun. Yönergeler, [Şirket içi konumlar ve bulut makaleleri arasında hareketli verilerde](data-factory-move-data-between-onprem-and-cloud.md) yer alır.

**Sybase bağlı hizmeti:**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure Blob depolama bağlı hizmeti:**

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Sybase giriş veri kümesi:**

Örnek, Sybase içinde bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestamp" adlı bir sütun içerdiğini varsayar.

"External" olarak ayarlanıyor: true, bu veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir. Bağlı hizmetin **türünün** şu şekilde ayarlandığından emin olun: **relationaltable**.

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopyalama etkinliği içeren işlem hattı:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılan ve saatlik olarak çalışacak şekilde zamanlanan bir kopyalama etkinliği içerir. Ardışık düzen JSON tanımında **kaynak** türü, **relationalsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu, DBA 'daki verileri seçer. Veritabanında Siparişler tablosu.

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>Sybase için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi kopyalama etkinliği, aşağıdaki 2 adımlı yaklaşımla kaynak türlerindeki otomatik tür dönüştürmeleri, havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Sybase T-SQL ve T-SQL türlerini destekler. SQL Types 'tan .NET türüne bir eşleme tablosu için bkz. [Azure SQL Bağlayıcısı](data-factory-azure-sql-connector.md) makalesi.

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
