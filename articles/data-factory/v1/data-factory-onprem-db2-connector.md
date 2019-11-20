---
title: Azure Data Factory kullanarak DB2 'den veri taşıma
description: Azure Data Factory kopyalama etkinliğini kullanarak şirket içi DB2 veritabanından veri taşımayı öğrenin
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0d066e66e4b9600eb5734ef2f3c6031dbc44f17a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666595"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Azure Data Factory kopyalama etkinliğini kullanarak DB2 verilerini taşıyın
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-onprem-db2-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-db2.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de DB2 Bağlayıcısı](../connector-db2.md).


Bu makalede, şirket içi bir DB2 veritabanından bir veri deposuna veri kopyalamak için Azure Data Factory kopyalama etkinliğini nasıl kullanabileceğiniz açıklanır. Verileri [Data Factory veri taşıma etkinlikleri](data-factory-data-movement-activities.md#supported-data-stores-and-formats) makalesinde desteklenen bir havuz olarak listelenen herhangi bir depoya kopyalayabilirsiniz. Bu konu, kopyalama etkinliğini kullanarak veri hareketine genel bakış sunan ve desteklenen veri deposu kombinasyonlarını listeleyen Data Factory makalesinde oluşturulur. 

Data Factory Şu anda yalnızca bir DB2 veritabanından [desteklenen havuz veri deposuna](data-factory-data-movement-activities.md#supported-data-stores-and-formats)veri taşımayı destekliyor. Verileri diğer veri depolarından DB2 veritabanına taşıma desteklenmez.

## <a name="prerequisites"></a>Ön koşullar
Data Factory, [veri yönetimi ağ geçidini](data-factory-data-management-gateway.md)kullanarak ŞIRKET içi DB2 veritabanına bağlanmayı destekler. Verilerinizi taşımak için ağ geçidi veri işlem hattını ayarlamaya yönelik adım adım yönergeler için, [verileri Şirket içinden buluta taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesine bakın.

DB2, Azure IaaS VM üzerinde barındırıldığında bile bir ağ geçidi gerekir. Ağ geçidini, veri deposuyla aynı IaaS sanal makinesine yükleyebilirsiniz. Ağ Geçidi veritabanına bağlanabilcan, ağ geçidini farklı bir VM 'ye yükleyebilirsiniz.

Veri yönetimi ağ geçidi yerleşik bir DB2 sürücüsü sağladığından verileri DB2 'den kopyalamak için el ile bir sürücü yüklemeniz gerekmez.

> [!NOTE]
> Bağlantı ve ağ geçidi sorunlarını giderme hakkında ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) makalesi.


## <a name="supported-versions"></a>Desteklenen sürümler
Data Factory DB2 Bağlayıcısı, dağıtılmış Ilişkisel veritabanı mimarisi (DRDA) SQL Access Manager sürümleri 9, 10 ve 11 olan aşağıdaki IBM DB2 platformunu ve sürümlerini destekler:

* Z/ç için IBM DB2 sürüm 11,1
* Z/ç için IBM DB2 sürüm 10,1
* İ (AS400) sürüm 7,2 için IBM DB2
* İ (AS400) sürüm 7,1 için IBM DB2
* Linux, UNIX ve Windows (LUW) için IBM DB2 sürüm 11
* LUW için IBM DB2 sürüm 10,5
* LUW için IBM DB2 sürüm 10,1

> [!TIP]
> "Hata iletisini alırsanız, bir SQL deyimindeki yürütme isteğine karşılık gelen paket bulunamadı. SQLSTATE = 51002 SQLCODE =-805, "Bu neden, işletim sistemindeki normal kullanıcı için gerekli bir paket oluşturulmaz. Bu sorunu çözmek için DB2 sunucunuzun türü için şu yönergeleri izleyin:
> - I için DB2 (AS400): bir Power User 'ın kopyalama etkinliğini çalıştırmadan önce normal kullanıcı için koleksiyon oluşturmasına Izin verin. Koleksiyonu oluşturmak için şu komutu kullanın: `create collection <username>`
> - Z/OS veya LUW için DB2: yüksek ayrıcalıklı bir hesap kullanın--paket yetkilileri ve BIND, BINERDD olan bir Power User veya yönetici, kopyayı bir kez çalıştırmak için ortak izinlere yürütme IZNI verır. Gerekli paket, kopyalama sırasında otomatik olarak oluşturulur. Daha sonra, sonraki kopya çalışmalarınız için normal kullanıcıya dönebilirsiniz.

## <a name="getting-started"></a>Başlarken
Farklı araçlar ve API 'Ler kullanarak şirket içi DB2 veri deposundan veri taşımak için kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz: 

- İşlem hattı oluşturmanın en kolay yolu Azure Data Factory kopyalama Sihirbazı ' nı kullanmaktır. Kopyalama Sihirbazı 'nı kullanarak bir işlem hattı oluşturmaya yönelik hızlı bir anlatım için, bkz [: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md). 
- Ayrıca, Visual Studio, Azure PowerShell, Azure Resource Manager şablonu, .NET API ve REST API dahil bir işlem hattı oluşturmak için araçları da kullanabilirsiniz. Kopyalama etkinliği ile işlem hattı oluşturmaya yönelik adım adım yönergeler için, [kopyalama etkinliği öğreticisine](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)bakın. 

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için bağlı hizmetler oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturun. 
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem hattı oluşturun. 

Kopyalama Sihirbazı 'nı kullandığınızda, Data Factory bağlı hizmetler, veri kümeleri ve işlem hattı varlıkları için JSON tanımları sizin için otomatik olarak oluşturulur. Araçları veya API 'Leri (.NET API hariç) kullandığınızda, Data Factory varlıklarını JSON biçimini kullanarak tanımlarsınız. JSON örneği: DB2 'den Azure Blob depolama 'ya veri kopyalama, şirket içi DB2 veri deposundan veri kopyalamak için kullanılan Data Factory varlıkların JSON tanımlarını gösterir.

Aşağıdaki bölümlerde, bir DB2 veri deposuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılar sağlanmaktadır.

## <a name="db2-linked-service-properties"></a>DB2 bağlı hizmeti özellikleri
Aşağıdaki tabloda, bir DB2 bağlantılı hizmetine özgü JSON özellikleri listelenmektedir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| **type** |Bu özellik **OnPremisesDb2**olarak ayarlanmalıdır. |Evet |
| **Server** |DB2 sunucusunun adı. |Evet |
| **veritabanınızı** |DB2 veritabanının adı. |Evet |
| **manızı** |DB2 veritabanındaki şemanın adı. Bu özellik büyük/küçük harfe duyarlıdır. |Hayır |
| **authenticationType** |DB2 veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: anonim, temel ve Windows. |Evet |
| **nitelen** |Temel veya Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı hesabının adı. |Hayır |
| **parolayı** |Kullanıcı hesabının parolası. |Hayır |
| **gatewayName** |Data Factory hizmetinin şirket içi DB2 veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Tüm veri kümesi türleri (Azure SQL, Azure Blob depolama, Azure Tablo depolama, vb.) için **Yapı**, **kullanılabilirlik**ve **ilke** gibi bölümler de benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. DB2 veri kümesini içeren **Relationaltable**türünde bir veri kümesinin **typeproperties** bölümü aşağıdaki özelliğe sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| **tableName** |DB2 veritabanı örneğindeki bağlı hizmetin başvurduğu tablonun adı. Bu özellik büyük/küçük harfe duyarlıdır. |Hayır ( **Relationalsource** türünde kopyalama etkinliğinin **sorgu** özelliği belirtilmişse) |

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri
Kopyalama etkinliklerini tanımlamaya yönelik bölümlerin ve özelliklerin bir listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. **Ad**, **Açıklama**, **giriş** tablosu, **Çıkış** tablosu ve **ilke**gibi kopyalama etkinliği özellikleri tüm etkinlik türleri için kullanılabilir. Etkinliğin **Typeproperties** bölümünde kullanılabilen özellikler her etkinlik türü için farklılık gösterir. Kopyalama etkinliği için özellikler veri kaynağı ve havuz türlerine göre değişir.

Kopyalama etkinliği için, kaynak **Relationalsource** (DB2 dahil) türünde olduğunda, **typeproperties** bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| **sorgulayamadı** |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin, `"query": "select * from "MySchema"."MyTable""` |Hayır (bir veri kümesinin **TableName** özelliği belirtilmişse) |

> [!NOTE]
> Şema ve tablo adları büyük/küçük harfe duyarlıdır. Sorgu ifadesinde, "" (çift tırnak) kullanarak özellik adlarını çevrelemek.

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON örneği: DB2 'den Azure Blob depolamaya veri kopyalama
Bu örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)'yu kullanarak bir işlem hattı oluşturmak için KULLANABILECEĞINIZ örnek JSON tanımlarını sağlar veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Örnek, bir DB2 veritabanından blob depolamaya nasıl veri kopyalanacağını gösterir. Ancak, veriler Azure Data Factory kopyalama etkinliği kullanılarak [desteklenen herhangi bir veri deposu havuzu türüne](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopyalanabilir.

Örnek aşağıdaki Data Factory varlıklara sahiptir:

- [OnPremisesDb2](data-factory-onprem-db2-connector.md) türünde bir DB2 bağlı hizmeti
- [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties) türünde bir Azure Blob depolama bağlı hizmeti
- [Relationaltable](data-factory-onprem-db2-connector.md#dataset-properties) türünde bir giriş [veri kümesi](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) türünde bir çıkış [veri kümesi](data-factory-create-datasets.md)
- [Relationalsource](data-factory-onprem-db2-connector.md#copy-activity-properties) ve [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) özelliklerini kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md)

Örnek, bir sorgudaki verileri bir DB2 veritabanında saatlik olarak bir Azure blobuna kopyalar. Örnekte kullanılan JSON özellikleri, varlık tanımlarını izleyen bölümlerde açıklanmıştır.

İlk adım olarak, bir veri ağ geçidi yükleyip yapılandırın. Yönergeler, [Şirket içi konumlar ve bulut makaleleri arasında hareketli verilerde](data-factory-move-data-between-onprem-and-cloud.md) yer alır.

**DB2 bağlı hizmeti**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**Azure Blob depolama bağlı hizmeti**

```json
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

**DB2 giriş veri kümesi**

Örnek, DB2 içinde, zaman serisi verileri için "timestamp" etiketli bir sütun içeren "MyTable" adlı bir tablo oluşturduğunuzu varsayar.

**External** özelliği "true" olarak ayarlanır. Bu ayar, bu veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir. **Type** özelliğinin **relationaltable**olarak ayarlandığını unutmayın.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Azure Blob çıktı veri kümesi**

**Sıklık** özelliği "Hour" ve **Interval** özelliği 1 olarak ayarlanarak, veriler her saat yeni bir bloba yazılır. Blob için **FolderPath** özelliği, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopyalama etkinliği için işlem hattı**

İşlem hattı, belirtilen giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış ve her saat çalışacak şekilde zamanlanan bir kopyalama etkinliği içerir. İşlem hattının JSON tanımında, **kaynak** türü **relationalsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu "Orders" tablosundan verileri seçer.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>DB2 için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi, kopyalama etkinliği aşağıdaki iki adımlı yaklaşımı kullanarak kaynak türünden havuz türüne otomatik tür dönüştürmeleri gerçekleştirir:

1. Yerel bir kaynak türünden .NET türüne dönüştürme
2. .NET türünden yerel havuz türüne dönüştürme

Kopyalama etkinliği verileri bir DB2 türünden .NET türüne dönüştürdüğünde aşağıdaki eşlemeler kullanılır:

| DB2 veritabanı türü | .NET Framework türü |
| --- | --- |
| Small |Int16 |
| Tamsayı |Int32 |
| BigInt |Int64 |
| Real |Tek |
| Çift |Çift |
| Float |Çift |
| Kategori |Kategori |
| DecimalFloat |Kategori |
| rakamlardan |Kategori |
| Tarih |DateTime |
| Zaman |TimeSpan |
| Zaman damgası |DateTime |
| 'Sini |Byte [] |
| Char |Dize |
| VarChar |Dize |
| LongVarChar |Dize |
| DB2DynArray |Dize |
| ý |Byte [] |
| Ikili |Byte [] |
| LongVarBinary |Byte [] |
| Sel |Dize |
| VarGraphic |Dize |
| LongVarGraphic |Dize |
| CLOB |Dize |
| Blob |Byte [] |
| DbClob |Dize |
| Small |Int16 |
| Tamsayı |Int32 |
| BigInt |Int64 |
| Real |Tek |
| Çift |Çift |
| Float |Çift |
| Kategori |Kategori |
| DecimalFloat |Kategori |
| rakamlardan |Kategori |
| Tarih |DateTime |
| Zaman |TimeSpan |
| Zaman damgası |DateTime |
| 'Sini |Byte [] |
| Char |Dize |

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunların havuz veri kümesindeki sütunlara nasıl eşlendiğini öğrenmek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>İlişkisel kaynaklardan tekrarlanabilir okuma
İlişkisel bir veri deposundan verileri kopyaladığınızda, istenmeyen sonuçlardan kaçınmak için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda bir dilimi yeniden çalıştırmak için bir veri kümesi için yeniden deneme **ilkesi** özelliğini de yapılandırabilirsiniz. Aynı verilerin, dilimin kaç kez yeniden çalıştırıldığına ve dilimi yeniden çalıştırma şeklinden bağımsız olarak okuduğunuzdan emin olun. Daha fazla bilgi için bkz. [ilişkisel kaynaklardan tekrarlanabilir okumalar](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayar
Kopyalama etkinliğinin performansını etkileyen anahtar faktörleri ve [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md)' nda performansı iyileştirme yolları hakkında bilgi edinin.
