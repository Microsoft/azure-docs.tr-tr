---
title: Azure Data Factory kullanarak DB2 'den veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak DB2 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: jingwang
ms.openlocfilehash: 642f12386a7695e026eb0c30016acf6f53fc9e95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381129"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Azure Data Factory kullanarak DB2 'den veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-onprem-db2-connector.md)
> * [Güncel sürüm](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, bir DB2 veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu DB2 veritabanı Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

DB2 veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu DB2 Bağlayıcısı, dağıtılmış Ilişkisel veritabanı mimarisi (DRDA) SQL Access Manager (SQLAM) sürüm 9, 10 ve 11 ile aşağıdaki IBM DB2 platformunu ve sürümlerini destekler.  DDM/DRDA protokolünü kullanır.

* Z/OS 12,1 için IBM DB2
* Z/OS 11,1 için IBM DB2
* Z/OS 10,1 için IBM DB2
* I 7,3 için IBM DB2
* I 7,2 için IBM DB2
* I 7,1 için IBM DB2
* LUW 11 için IBM DB2
* LUW 10,5 için IBM DB2
* LUW 10,1 için IBM DB2

>[!TIP]
>DB2 Bağlayıcısı DB2 için Microsoft OLE DB Sağlayıcısı üzerine kurulmuştur. DB2 bağlayıcı hatalarıyla ilgili sorunları gidermek için [veri sağlayıcısı hata kodlarına](/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)bakın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime yerleşik bir DB2 sürücüsü sağlar, bu nedenle DB2 'den veri kopyalarken herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler DB2 bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler DB2 bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **DB2** olarak ayarlanmalıdır | Yes |
| Dizisi | DB2 örneğine bağlanmak için gereken bilgileri belirtin.<br/> Ayrıca, Azure Key Vault parolayı yerleştirebilir ve `password` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

Bağlantı dizesinin içindeki tipik Özellikler:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| sunucu |DB2 sunucusunun adı. Sunucu adının sonuna iki nokta üst üste ile ayrılmış bağlantı noktası numarasını belirtebilirsiniz, örneğin `server:port` .<br>DB2 Bağlayıcısı DDM/DRDA protokolünü kullanır ve belirtilmemişse varsayılan olarak 50000 numaralı bağlantı noktasını kullanır. Belirli DB2 veritabanınızın kullandığı bağlantı noktası, sürüme ve ayarlarınıza göre farklı olabilir. Örneğin, DB2 LUW, AS400 için varsayılan bağlantı noktası 50000, varsayılan bağlantı noktası 446 ' dir ve TLS etkinleştirildiğinde 448. Bağlantı noktasının genellikle nasıl yapılandırıldığı hakkında aşağıdaki DB2 belgelerine başvurun: [DB2 z/OS](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.5.0/com.ibm.db2.luw.qb.dbconn.doc/doc/t0008229.html), [DB2 iSeries](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_74/ddp/rbal1ports.htm)ve [db2 LUW](https://www.ibm.com/support/knowledgecenter/en/SSEKCU_1.1.3.0/com.ibm.psc.doc/install/psc_t_install_typical_db2_port.html). |Yes |
| database |DB2 veritabanının adı. |Yes |
| authenticationType |DB2 veritabanına bağlanmak için kullanılan kimlik doğrulaması türü.<br/>İzin verilen değer: **temel**. |Yes |
| username |DB2 veritabanına bağlanmak için Kullanıcı adını belirtin. |Yes |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Yes |
| packageCollection    | Veritabanı sorgulanırken, gerekli paketlerin ADF tarafından otomatik olarak oluşturulduğu yeri belirtin. Bu ayarlanmamışsa, Data Factory varsayılan değer olarak {username} kullanır. | No |
| Certificatecommonadı | Güvenli Yuva Katmanı (SSL) veya Aktarım Katmanı Güvenliği (TLS) şifrelemesini kullandığınızda, sertifika ortak adı için bir değer girmeniz gerekir. | No |

> [!TIP]
> Durumu belirten bir hata iletisi alırsanız, bunun `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805` nedeni Kullanıcı için gerekli bir paket oluşturulmaz. Varsayılan olarak, ADF, DB2 'ye bağlanmak için kullandığınız kullanıcı olarak adlı koleksiyonda bir paket oluşturmaya çalışır. ADF 'nin veritabanını sorgularken gereken paketleri oluşturmasını istediğiniz yeri belirtmek için paket koleksiyonu özelliğini belirtin.

**Örnek:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;password=<password>;packageCollection=<packagecollection>;certificateCommonName=<certname>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
**Örnek: Azure Key Vault parola depola**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "connectionString": "server=<server:port>;database=<database>;authenticationType=Basic;username=<username>;packageCollection=<packagecollection>;certificateCommonName=<certname>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Aşağıdaki yük ile DB2 bağlı hizmeti kullanıyorsanız, yeni bir tane olduğu gibi, ileri ' yi kullanmanız önerilir.

**Önceki yük:**

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
| tür | DataSet 'in Type özelliği: **Db2Table** olarak ayarlanmalıdır | Yes |
| schema | Şemanın adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablo | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Şemanın bulunduğu tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. `schema` `table` Yeni iş yükü için ve kullanın. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

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

`RelationalTable`Türü belirtilmiş veri kümesi kullanıyorsanız, hala olduğu gibi desteklenir, ancak yeni bir adım ileri kullanmanız önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, DB2 kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="db2-as-source"></a>Kaynak olarak DB2

DB2 'den veri kopyalamak için aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Db2Source** olarak ayarlanmalıdır | Yes |
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

Yazılan kaynağı kullanıyorsanız, `RelationalSource` hala olduğu gibi desteklenmektedir, ileri ' yi kullanmaya devam etmeniz önerilir.

## <a name="data-type-mapping-for-db2"></a>DB2 için veri türü eşlemesi

DB2 'den veri kopyalarken aşağıdaki eşlemeler DB2 veri türlerinden, geçici veri türlerini Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| DB2 veritabanı türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| BigInt |Int64 |
| İkili |Byte [] |
| Blob |Byte [] |
| Char |Dize |
| CLOB |Dize |
| Tarih |Tarih saat |
| DB2DynArray |Dize |
| DbClob |Dize |
| Ondalık |Ondalık |
| DecimalFloat |Ondalık |
| Çift |Çift |
| Float |Çift |
| Sel |Dize |
| Tamsayı |Int32 |
| LongVarBinary |Byte [] |
| LongVarChar |Dize |
| LongVarGraphic |Dize |
| Sayısal |Ondalık |
| Gerçek |Tek |
| Small |Int16 |
| Saat |TimeSpan |
| Timestamp |DateTime |
| Ikili |Byte [] |
| VarChar |Dize |
| VarGraphic |Dize |
| Xml |Byte [] |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).