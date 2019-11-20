---
title: Azure Data Factory kullanarak ODBC kaynaklarından veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak OData kaynaklarından desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
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
ms.openlocfilehash: c597d8a97e024fc34e8df16dfcbffef41d8f765f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680608"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Azure Data Factory kullanarak verileri ve ODBC veri depolarından veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-odbc-connector.md)
> * [Geçerli sürüm](connector-odbc.md)

Bu makalede, verileri bir ODBC veri deposuna ve ' den kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu ODBC Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

ODBC kaynağından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilir veya desteklenen herhangi bir kaynak veri deposundan ODBC havuzuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu ODBC Bağlayıcısı **temel** veya **anonim** kimlik doğrulaması kullanarak **ODBC ile uyumlu tüm veri depolarından** veri kopyalamayı destekler.

## <a name="prerequisites"></a>Ön koşullar

Bu ODBC bağlayıcısını kullanmak için şunları yapmanız gerekir:

- Şirket içinde barındırılan bir Integration Runtime ayarlayın. Ayrıntılar için bkz. [Şirket içinde barındırılan Integration Runtime](create-self-hosted-integration-runtime.md) makalesi.
- Integration Runtime makinesindeki veri deposu için ODBC sürücüsünü yükler.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, ODBC bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

ODBC bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **ODBC** olarak ayarlanmalıdır | Evet |
| connectionString | Kimlik bilgisi bölümünü dışlayarak bağlantı dizesi. Bağlantı dizesini `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`gibi bir düzende belirtebilir veya `"DSN=<name of the DSN on IR machine>;"` ile Integration Runtime makinede ayarladığınız Sistem DSN 'sini (veri kaynağı adı) kullanabilirsiniz (yine de bağlı hizmette kimlik bilgisi bölümünü buna göre belirtmeniz gerekir).<br>Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın.| Evet |
| authenticationType | ODBC veri deposuna bağlanmak için kullanılan kimlik doğrulaması türü.<br/>İzin verilen değerler: **temel** ve **anonim**. | Evet |
| Nitelen | Temel kimlik doğrulaması kullanıyorsanız Kullanıcı adını belirtin. | Hayır |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Hayır |
| kimlik bilgisi | Sürücüye özgü özellik-değer biçiminde belirtilen bağlantı dizesinin erişim kimlik bilgisi kısmı. Örnek: `"RefreshToken=<secret refresh token>;"`. Bu alanı SecureString olarak işaretleyin. | Hayır |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşul](#prerequisites)bölümünde belirtildiği gibi, kendinden konak Integration Runtime gereklidir. |Evet |

**Örnek 1: temel kimlik doğrulaması kullanma**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Örnek 2: anonim kimlik doğrulaması kullanma**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, ODBC veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

ODBC ile uyumlu veri deposundan veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **Odbctable** olarak ayarlanmalıdır | Evet |
| tableName | ODBC veri deposundaki tablonun adı. | Kaynak için Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse);<br/>Havuz için Evet |

**Örnek**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Türü belirlenmiş `RelationalTable` veri kümesini kullanıyorsanız, bunun olduğu gibi hala desteklenmektedir, ileri ' yi kullanmaya devam etmeniz önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, ODBC kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="odbc-as-source"></a>Kaynak olarak ODBC

ODBC uyumlu veri deposundan verileri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **Odbcsource** olarak ayarlanmalıdır | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM MyTable"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Türü belirlenmiş `RelationalSource` kaynak kullanıyorsanız, hala olduğu gibi desteklenirken, ileri ' yi kullanmanız önerilir.

### <a name="odbc-as-sink"></a>Havuz olarak ODBC

ODBC uyumlu veri deposuna veri kopyalamak için kopyalama etkinliğindeki havuz türünü **Odbcsink**olarak ayarlayın. Aşağıdaki özellikler, kopyalama etkinliği **havuzu** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuzunun Type özelliği: **Odbcsink** olarak ayarlanmalıdır | Evet |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi.<br/>İzin verilen değerler: TimeSpan. Örnek: "00:30:00" (30 dakika). |Hayır |
| writeBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler.<br/>İzin verilen değerler: Integer (satır sayısı). |Hayır (varsayılan 0-otomatik olarak algılanır) |
| Ön Copyscrıpt |Her çalıştırmada veri deposuna veri yazmadan önce yürütülecek kopyalama etkinliği için bir SQL sorgusu belirtin. Bu özelliği, önceden yüklenmiş verileri temizlemek için kullanabilirsiniz. |Hayır |

> [!NOTE]
> "WriteBatchSize" ayarlanmamışsa (otomatik algılanan), kopyalama etkinliği önce sürücünün Batch işlemlerini destekleyip desteklemediğini algılar ve bunu yoksa 10000 olarak ayarlayın veya yoksa 1 olarak ayarlayın. 0 dışında bir değer ayarlarsanız, kopyalama etkinliği değeri eşit kılar ve sürücü Batch işlemlerini desteklemiyorsa çalışma zamanında başarısız olur.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>SAP HANA havuzu

>[!NOTE]
>SAP HANA veri deposundan verileri kopyalamak için yerel [SAP HANA Bağlayıcısı](connector-sap-hana.md)' na bakın. SAP HANA verileri kopyalamak için lütfen bu yönergeyi izleyerek ODBC bağlayıcısını kullanın. SAP HANA Bağlayıcısı ve ODBC Bağlayıcısı için bağlı hizmetlerin farklı türde olduğunu ve bu nedenle yeniden kullanılmamasını aklınızda olun.
>

Genel ODBC bağlayıcısını kullanarak verileri SAP HANA veritabanına kopyalayabilirsiniz.

Veri deponuza erişimi olan bir makinede şirket içinde barındırılan bir Integration Runtime ayarlayın. Integration Runtime, veri deposuna bağlanmak için SAP HANA ODBC sürücüsünü kullanır. Bu nedenle, aynı makinede zaten yüklü değilse sürücüyü yükleme. Ayrıntılar için [Önkoşullar](#prerequisites) bölümüne bakın.

Data Factory çözümünde SAP HANA havuzunu kullanmadan önce, Integration Runtime [bağlantı sorunlarını giderme](#troubleshoot-connectivity-issues) bölümündeki yönergeleri kullanarak veri deposuna bağlanıp bağlanamayacağını doğrulayın.

Aşağıdaki örnekte gösterildiği gibi SAP HANA veri deposunu bir Azure Data Factory 'ye bağlamak için ODBC bağlantılı hizmeti oluşturun:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

ODBC veri depolarının bir kopyalama işleminde kaynak/havuz veri depoları olarak kullanılmasına ilişkin ayrıntılı bir genel bakış için makalenin başındaki makaleyi okuyun.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="troubleshoot-connectivity-issues"></a>Bağlantı sorunlarını giderme

Bağlantı sorunlarını gidermek için **Integration Runtime Configuration Manager** **Tanılama** sekmesini kullanın.

1. **Integration Runtime Configuration Manager**başlatın.
2. **Tanılama** sekmesine geçin.
3. "Bağlantıyı Sına" bölümünde, veri deposu **türünü** (bağlı hizmet) seçin.
4. Veri deposuna bağlanmak için kullanılan **bağlantı dizesini** belirtin, **kimlik doğrulamasını** seçin ve **Kullanıcı adı**, **parola**ve/veya **kimlik bilgilerini**girin.
5. Veri deposuyla bağlantıyı test etmek için **Bağlantıyı Sına** ' ya tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
