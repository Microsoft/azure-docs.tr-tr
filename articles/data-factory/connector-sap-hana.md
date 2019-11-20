---
title: Azure Data Factory kullanarak SAP HANA verileri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliği kullanarak SAP HANA verileri desteklenen havuz verileri depolarına kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 6b7f41f53ea743f8e3914512b40d3f69f595b7c8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680247"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP HANA verileri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-sap-hana-connector.md)
> * [Geçerli sürüm](connector-sap-hana.md)

Bu makalede, SAP HANA veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için ayrıntılı giriş, comparme ve kılavuzla [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu SAP HANA Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

SAP HANA veritabanından, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu SAP HANA Bağlayıcısı şunları destekler:

- SAP HANA veritabanının herhangi bir sürümünden veri kopyalanıyor.
- **Hana bilgi modellerinden** (analitik ve hesaplama görünümleri gibi) ve **satır/sütun tablolarının**verilerini kopyalama.
- **Temel** veya **Windows** kimlik doğrulaması kullanarak verileri kopyalama.

> [!TIP]
> Verileri **SAP HANA veri** deposuna kopyalamak IÇIN Genel ODBC Bağlayıcısı ' nı kullanın. Ayrıntılara [SAP HANA havuza](connector-odbc.md#sap-hana-sink) bakın. SAP HANA Bağlayıcısı ve ODBC Bağlayıcısı için bağlı hizmetlerin farklı türde olduğunu ve bu nedenle yeniden kullanılmamasını aklınızda olun.

## <a name="prerequisites"></a>Ön koşullar

Bu SAP HANA bağlayıcısını kullanmak için şunları yapmanız gerekir:

- Şirket içinde barındırılan bir Integration Runtime ayarlayın. Ayrıntılar için bkz. [Şirket içinde barındırılan Integration Runtime](create-self-hosted-integration-runtime.md) makalesi.
- SAP HANA ODBC sürücüsünü Integration Runtime makinesine yükler. SAP HANA ODBC sürücüsünü [SAP Software Download Center](https://support.sap.com/swdc)'dan indirebilirsiniz. **Windows için SAP HANA**anahtar sözcüğünü kullanarak arama yapın.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler SAP HANA bağlayıcıya özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

SAP HANA bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **Saphana** olarak ayarlanmalıdır | Evet |
| connectionString | **Temel kimlik doğrulaması** veya **Windows kimlik doğrulaması**kullanarak SAP HANA bağlanmak için gereken bilgileri belirtin. Aşağıdaki örneklere bakın.<br>Bağlantı dizesinde, sunucu/bağlantı noktası zorunludur (varsayılan bağlantı noktası 30015 ' dir), temel kimlik doğrulaması kullanılırken Kullanıcı adı ve parola zorunludur. Ek Gelişmiş ayarlar için [SAP HANA ODBC bağlantı özellikleri](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) ' ne bakın.<br/>Parolayı Azure Key Vault de yerleştirebilir ve parola yapılandırmasını bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için [Azure Key Vault makalesinde mağaza kimlik bilgilerini](store-credentials-in-key-vault.md) inceleyin. | Evet |
| Nitelen | Windows kimlik doğrulaması kullanırken kullanıcı adını belirtin. Örnek: `user@domain.com` | Hayır |
| password | Kullanıcı hesabı için parola belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Hayır |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşul](#prerequisites)bölümünde belirtildiği gibi, kendinden konak Integration Runtime gereklidir. |Evet |

**Örnek: temel kimlik doğrulaması kullanma**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Windows kimlik doğrulamasını kullanma**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
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

Aşağıdaki yük ile SAP HANA bağlı hizmeti kullanıyorsanız, hala olduğu gibi desteklenirken, ileri ' yi kullanmanız önerilir.

**Örnek:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
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

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm SAP HANA veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

SAP HANA verileri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği: **Saphanatable** olarak ayarlanmalıdır | Evet |
| manızı | SAP HANA veritabanındaki şemanın adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |
| tablosundan | SAP HANA veritabanındaki tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Türü belirlenmiş `RelationalTable` veri kümesini kullanıyorsanız, bunun olduğu gibi hala desteklenmektedir, ileri ' yi kullanmaya devam etmeniz önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm SAP HANA kaynak tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sap-hana-as-source"></a>Kaynak olarak SAP HANA

SAP HANA verileri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **Saphanasource** olarak ayarlanmalıdır | Evet |
| sorgu | SAP HANA örneğinden verileri okumak için SQL sorgusunu belirtir. | Evet |
| packetSize | Verilerin birden çok bloğuyla bölüneceği ağ paketi boyutunu (kilobayt olarak) belirtir. Kopyalanacak büyük miktarda veriniz varsa, paket boyutunu artırmak çoğu durumda SAP HANA okuma hızını artırabilir. Paket boyutu ayarlanırken performans testi önerilir. | Hayır.<br>Varsayılan değer 2048 ' dir (2MB). |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Türü belirtilmiş `RelationalSource` kopyalama kaynağı kullanıyorsanız, yeni bir tane olduğu gibi, ileri ' yi kullanmanız önerilir.

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA için veri türü eşlemesi

SAP HANA verileri kopyalarken, SAP HANA veri türlerinden aşağıdaki eşlemeler Azure Data Factory geçici veri türlerini kullanır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| SAP HANA veri türü | Veri Fabrikası geçici veri türü |
| ------------------ | ------------------------------ |
| ALHAYALI um           | Dize                         |
| BIGıNT             | Int64                          |
| BINARY             | Byte []                         |
| BINTEXT            | Dize                         |
| BLOB               | Byte []                         |
| BOOL               | Bayt                           |
| CLOB               | Dize                         |
| DATE               | DateTime                       |
| DECIMAL            | Kategori                        |
| ÇIFT             | Çift                         |
| FLOAT              | Çift                         |
| INTEGER            | Int32                          |
| NCLOB              | Dize                         |
| NVARCHAR           | Dize                         |
| GERÇEK               | Tek                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Dize                         |
| SMALLDECıMAL       | Kategori                        |
| Small           | Int16                          |
| STGEOMETRYTYPE     | Byte []                         |
| STPOINTTYPE        | Byte []                         |
| METINLERI               | Dize                         |
| TIME               | TimeSpan                       |
| Iç            | Bayt                           |
| VARCHAR            | Dize                         |
| ILIŞKIN          | DateTime                       |
| VARBINARY          | Byte []                         |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
