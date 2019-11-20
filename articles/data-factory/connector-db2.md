---
title: Azure Data Factory kullanarak DB2 'den veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak DB2 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: dad28da0b481467633bebf664fea2be39a50200b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681045"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Azure Data Factory kullanarak DB2 'den veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-onprem-db2-connector.md)
> * [Geçerli sürüm](connector-db2.md)

Bu makalede, bir DB2 veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu DB2 veritabanı Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

DB2 veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu DB2 Bağlayıcısı, dağıtılmış Ilişkisel veritabanı mimarisi (DRDA) SQL Access Manager (, 10 ve 11) ile birlikte aşağıdaki IBM DB2 platformunu ve sürümlerini destekler:

* Z/OS 12,1 için IBM DB2
* Z/OS 11,1 için IBM DB2
* Z/OS 10,1 için IBM DB2
* I 7,3 için IBM DB2
* I 7,2 için IBM DB2
* I 7,1 için IBM DB2
* LUW 11 için IBM DB2
* LUW 10,5 için IBM DB2
* LUW 10,1 için IBM DB2

> [!TIP]
> "SQL deyimindeki bir yürütme isteğine karşılık gelen paket bulunamadı" iletisini alırsanız bir hata mesajı alırsanız. SQLSTATE = 51002 SQLCODE =-805 ", bu nedenle söz konusu IŞLETIM sisteminde normal kullanıcı için gerekli bir paket oluşturulmaz. Bu yönergeleri DB2 sunucunuzun türüne göre izleyin:
> - I için DB2 (AS400): kopyalama etkinliğini kullanmadan önce, Power User 'ın oturum açma kullanıcısı için koleksiyon oluşturmasına izin verin. Komut: `create collection <username>`
> - Z/OS veya LUW için DB2: paket yetkilileri ve bağlama, BINERDD ve BIND, BINERDD ile yüksek ayrıcalıklı bir hesap-Power User veya admin kullanın-kopyalama etkinliğini bir kez çalıştırmak için, gerekli paket kopyalama sırasında otomatik olarak oluşturulur. Daha sonra, sonraki kopya çalışmalarınız için normal kullanıcıya dönebilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime yerleşik bir DB2 sürücüsü sağlar, bu nedenle DB2 'den veri kopyalarken herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler DB2 bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler DB2 bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **DB2** olarak ayarlanmalıdır | Evet |
| sunucu |DB2 sunucusunun adı. Sunucu adının sonuna iki nokta (`server:port`) ile ayrılmış bağlantı noktası numarasını belirtebilirsiniz. |Evet |
| veritabanı |DB2 veritabanının adı. |Evet |
| authenticationType |DB2 veritabanına bağlanmak için kullanılan kimlik doğrulaması türü.<br/>İzin verilen değer: **temel**. |Evet |
| kullanıcı adı |DB2 veritabanına bağlanmak için Kullanıcı adını belirtin. |Evet |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

**Örnek:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, DB2 veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

DB2 'den veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **Db2Table** olarak ayarlanmalıdır | Evet |
| manızı | Şemanın adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablosundan | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Şemanın bulunduğu tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve `table` kullanın. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Türü belirlenmiş `RelationalTable` veri kümesini kullanıyorsanız, bunun olduğu gibi hala desteklenmektedir, ileri ' yi kullanmaya devam etmeniz önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, DB2 kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="db2-as-source"></a>Kaynak olarak DB2

DB2 'den veri kopyalamak için aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **Db2Source** olarak ayarlanmalıdır | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Türü belirlenmiş `RelationalSource` kaynak kullanıyorsanız, hala olduğu gibi desteklenirken, ileri ' yi kullanmanız önerilir.

## <a name="data-type-mapping-for-db2"></a>DB2 için veri türü eşlemesi

DB2 'den veri kopyalarken aşağıdaki eşlemeler DB2 veri türlerinden, geçici veri türlerini Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| DB2 veritabanı türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| BigInt |Int64 |
| ý |Byte [] |
| Blob |Byte [] |
| Char |Dize |
| CLOB |Dize |
| Tarih |Hem |
| DB2DynArray |Dize |
| DbClob |Dize |
| Kategori |Kategori |
| DecimalFloat |Kategori |
| Çift |Çift |
| Float |Çift |
| Sel |Dize |
| Tamsayı |Int32 |
| LongVarBinary |Byte [] |
| LongVarChar |Dize |
| LongVarGraphic |Dize |
| rakamlardan |Kategori |
| Real |Tek |
| Small |Int16 |
| Zaman |TimeSpan |
| Zaman damgası |DateTime |
| Ikili |Byte [] |
| VarChar |Dize |
| VarGraphic |Dize |
| 'Sini |Byte [] |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
