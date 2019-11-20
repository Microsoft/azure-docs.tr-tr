---
title: Azure Data Factory kullanarak PostgreSQL 'Ten veri taşıma
description: Azure Data Factory kullanarak PostgreSQL veritabanından veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6d8c63551bd6bcc7a7e00dffa6c2b6d9e0e644db
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666079"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Azure Data Factory kullanarak PostgreSQL 'ten veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-onprem-postgresql-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-postgresql.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de PostgreSQL Bağlayıcısı](../connector-postgresql.md)' na bakın.


Bu makalede, verileri şirket içi PostgreSQL veritabanından taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Şirket içi bir PostgreSQL veri deposundan, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory Şu anda verileri bir PostgreSQL veritabanından diğer veri depolarına taşımayı destekler, ancak diğer veri depolarından verileri bir PostgreSQL veritabanına taşımaya yönelik değildir.

## <a name="prerequisites"></a>Ön koşullar

Data Factory hizmeti, Veri Yönetimi ağ geçidini kullanarak şirket içi PostgreSQL kaynaklarına bağlanmayı destekler. Veri Yönetimi ağ geçidini ayarlama hakkında bilgi edinmek ve ağ geçidini ayarlamaya yönelik adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makaleleri arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

PostgreSQL veritabanı bir Azure IaaS VM 'sinde barındırıldığında bile ağ geçidi gereklidir. Ağ geçidini, veri deposuyla aynı IaaS sanal makinesine veya ağ geçidinin veritabanına bağlanabildiği sürece farklı bir VM 'ye yükleyebilirsiniz.

> [!NOTE]
> Bağlantı/ağ geçidi ile ilgili sorunları gidermeye yönelik ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Desteklenen sürümler ve yükleme
PostgreSQL veritabanına bağlanmak üzere Veri Yönetimi ağ geçidinin, 2.0.12 ve 3.1.9 arasında sürümü ile [PostgreSQL Için Ngpsql veri sağlayıcısı](https://go.microsoft.com/fwlink/?linkid=282716) 'Nı veri yönetimi ağ geçidi ile aynı sistemde yükleme yapın. PostgreSQL sürüm 7,4 ve üzeri desteklenir.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak şirket içi PostgreSQL veri deposundan veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .
- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz:
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager şablonu
  - .NET API’si
  - REST API

    Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Şirket içi bir PostgreSQL veri deposundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarının bulunduğu bir örnek için, bu makaledeki [JSON örneği: PostgreSQL 'Den Azure Blob 'a veri kopyalama](#json-example-copy-data-from-postgresql-to-azure-blob) bölümüne bakın.

Aşağıdaki bölümler, bir PostgreSQL veri deposuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, PostgreSQL bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **OnPremisesPostgreSql** olarak ayarlanmalıdır |Evet |
| sunucu |PostgreSQL sunucusunun adı. |Evet |
| veritabanı |PostgreSQL veritabanının adı. |Evet |
| manızı |Veritabanındaki şemanın adı. Şema adı büyük/küçük harfe duyarlıdır. |Hayır |
| authenticationType |PostgreSQL veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim, temel ve Windows. |Evet |
| kullanıcı adı |Temel veya Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |
| gatewayName |Data Factory hizmetinin şirket içi PostgreSQL veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Relationaltable** türündeki (PostgreSQL veri kümesini içeren) veri kümesinin typeproperties bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu PostgreSQL veritabanı örneğindeki tablonun adı. TableName, büyük/küçük harfe duyarlıdır. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Kaynak, **Relationalsource** türünde olduğunda (PostgreSQL içeren), typeproperties bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: `"query": "select * from \"MySchema\".\"MyTable\""`. |Hayır ( **veri kümesi** **TableName** belirtilmişse) |

> [!NOTE]
> Şema ve tablo adları büyük/küçük harfe duyarlıdır. Onları sorguya `""` (çift tırnak) içine alın.

**Örnek:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON örneği: PostgreSQL 'ten Azure Blob 'a veri kopyalama
Bu örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. PostgreSQL veritabanından Azure Blob depolama alanına nasıl veri kopyalanacağını gösterir. Ancak, veriler burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza kopyalanabilir.

> [!IMPORTANT]
> Bu örnek, JSON parçacıkları sağlar. Veri Fabrikası oluşturmaya yönelik adım adım yönergeler içermez. Adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makalesi arasında verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

1. [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Relationaltable](data-factory-onprem-postgresql-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Relationalsource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip işlem [hattı](data-factory-create-pipelines.md) .

Örnek, PostgreSQL veritabanında bulunan bir sorgudan verileri her saat bir bloba kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini ayarlayın. Yönergeler, [Şirket içi konumlar ve bulut makaleleri arasında hareketli verilerde](data-factory-move-data-between-onprem-and-cloud.md) yer alır.

**PostgreSQL bağlı hizmeti:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
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

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**PostgreSQL giriş veri kümesi:**

Örnek, PostgreSQL içinde bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestamp" adlı bir sütun içerdiğini varsayar.

Ayar `"external": true`, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılan ve saatlik olarak çalışacak şekilde zamanlanan bir kopyalama etkinliği içerir. Ardışık düzen JSON tanımında **kaynak** türü, **relationalsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu, PostgreSQL veritabanındaki public. usstates tablosundan verileri seçer.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>PostgreSQL için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi, kopyalama etkinliği, kaynak türlerinden aşağıdaki 2 adımlı yaklaşımla otomatik tür dönüştürmeleri gerçekleştirir:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Verileri PostgreSQL 'e taşırken, PostgreSQL türünden .NET türüne aşağıdaki eşlemeler kullanılır.

| PostgreSQL veritabanı türü | PostgresSQL diğer adları | .NET Framework türü |
| --- | --- | --- |
| abstime | |Hem |
| bigint |Int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte [], dize |
| bit farklı [(n)] |varbit |Byte [], dize |
| boole |bool |Boole |
| Kutudaki | |Byte [], dize |
| bytea | |Byte [], dize |
| karakter [(n)] |Char [(n)] |Dize |
| karakter farklı [(n)] |varchar [(n)] |Dize |
| CID | |Dize |
| CIDR | |Dize |
| üstünde | |Byte [], dize |
| date | |Hem |
| dateRange | |Dize |
| çift duyarlık |float8 |Çift |
| inet | |Byte [], dize |
| ıntarry | |Dize |
| int4range | |Dize |
| int8range | |Dize |
| integer |int, int4 |Int32 |
| Aralık [Fields] [(p)] | |Timespan |
| nesnesinde | |Dize |
| jsonb | |Byte [] |
| satırı | |Byte [], dize |
| lseg | |Byte [], dize |
| macaddr | |Byte [], dize |
| etmenize | |Kategori |
| sayısal [(p, s)] |Decimal [(p, s)] |Kategori |
| numrange | |Dize |
| id | |Int32 |
| Yolun | |Byte [], dize |
| pg_lsn | |Int64 |
| Seçeneğinin | |Byte [], dize |
| Gen | |Byte [], dize |
| gerçek |float4 |Tek |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| ardışık |serial4 |Int32 |
| metin | |Dize |

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
