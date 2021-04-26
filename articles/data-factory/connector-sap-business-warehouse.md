---
title: SAP BW verileri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak SAP Business Warehouse 'tan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 4a5eb7c8d729fc0c3b95a61a4b8dbc8266d1eec3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386671"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP Business Warehouse 'tan veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Güncel sürüm](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, bir SAP Business Warehouse 'tan (siyah-beyaz) veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için, her SAP Bağlayıcısı, karşılaştırma ve kılavuza ayrıntılı giriş ile [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu SAP Business Warehouse Connector, aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

SAP Business Warehouse 'tan verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu SAP Business Warehouse Connector şunları destekler:

- SAP Business Warehouse **sürüm 7. x**.
- MDX sorgularını kullanarak **InfoCubes ve Queryküplerinden** (Bex sorguları dahil) verileri kopyalama.
- Temel kimlik doğrulaması kullanarak verileri kopyalama.

## <a name="prerequisites"></a>Önkoşullar

Bu SAP Business Warehouse bağlayıcısını kullanmak için şunları yapmanız gerekir:

- Şirket içinde barındırılan bir Integration Runtime ayarlayın. Ayrıntılar için bkz. [Şirket içinde barındırılan Integration Runtime](create-self-hosted-integration-runtime.md) makalesi.
- Integration Runtime makinesine **SAP NetWeaver kitaplığını** yüklersiniz. SAP NetWeaver kitaplığını SAP yöneticinizden alabilir veya doğrudan [SAP Software Download Center](https://support.sap.com/swdc)' dan edinebilirsiniz. En son sürüme ait indirme konumunu almak için **SAP Note #1025361** aratın. Integration Runtime yüklemenize uyan **64 bitlik** SAP NetWeaver kitaplığını seçtiğinizden emin olun. Daha sonra SAP NetWeaver RFC SDK 'sına dahil edilen tüm dosyaları SAP notuna göre yükler. SAP NetWeaver kitaplığı, SAP Istemci araçları yüklemesine de dahildir.

>[!TIP]
>SAP BW bağlantı sorununu gidermek için, şunları yaptığınızdan emin olun:
>- NetWeaver RFC SDK 'dan ayıklanan tüm bağımlılık kitaplıkları%Windir%\System32 klasöründe bulunur. Genellikle icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll vardır.
>- SAP sunucusuna bağlanmak için kullanılan bağlantı noktaları, genellikle bağlantı noktası 3300 ve 3201 olan şirket içinde barındırılan IR makinesinde etkinleştirilir.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, SAP Business Warehouse Connector 'a özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

SAP Business Warehouse (bant genişliği) bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Sapbeyaz** olarak ayarlanmalıdır | Yes |
| sunucu | SAP BW örneğinin bulunduğu sunucunun adı. | Yes |
| systemNumber | SAP BW sisteminin sistem numarası.<br/>İzin verilen değer: dize olarak temsil edilen iki basamaklı ondalık sayı. | Yes |
| clientId | SAP W sistemindeki istemcinin istemci KIMLIĞI.<br/>İzin verilen değer: dize olarak temsil edilen üç basamaklı ondalık sayı. | Yes |
| userName | SAP sunucusuna erişimi olan kullanıcının adı. | Yes |
| password | Kullanıcının parolası. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşul](#prerequisites)bölümünde belirtildiği gibi, kendinden konak Integration Runtime gereklidir. |Yes |

**Örnek:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm SAP BW veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

SAP BW verileri kopyalamak için, veri kümesinin Type özelliğini **Sapbwcube** olarak ayarlayın. RelationalTable türünde SAP BW veri kümesi için desteklenen türe özgü özellik yok.

**Örnek:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable`Türü belirtilmiş veri kümesi kullanıyorsanız, hala olduğu gibi desteklenir, ancak yeni bir adım ileri kullanmanız önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm SAP BW kaynak tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sap-bw-as-source"></a>Kaynak olarak SAP BW

SAP BW verileri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Sapbwsource** olarak ayarlanmalıdır | Yes |
| sorgu | SAP BW örneğinden verileri okumak için MDX sorgusunu belirtir. | Yes |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Yazılan kaynağı kullanıyorsanız, `RelationalSource` hala olduğu gibi desteklenmektedir, ileri ' yi kullanmaya devam etmeniz önerilir.

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW için veri türü eşlemesi

SAP BW verileri kopyalarken, SAP BW veri türlerinden aşağıdaki eşlemeler Azure Data Factory geçici veri türlerini kullanır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| SAP BW veri türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| ACCP | int |
| CHAR | Dize |
| CLNT | Dize |
| CURR | Ondalık |
| CUKY | Dize |
| DEC | Ondalık |
| FLTP | Çift |
| INT1 | Bayt |
| INT2 | Int16 |
| INT4 | int |
| LANG | Dize |
| LCHR | Dize |
| LRAW | Byte [] |
| PREC | Int16 |
| QUA | Ondalık |
| Madde | Byte [] |
| RAWSTRING | Byte [] |
| DIZISINDE | Dize |
| BIRIM | Dize |
| KATALAR | Dize |
| NUMC | Dize |
| TIMS | Dize |


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
