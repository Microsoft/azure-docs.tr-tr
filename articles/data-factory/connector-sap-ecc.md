---
title: Azure Data Factory kullanarak SAP ECC 'den veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak SAP ECC 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
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
ms.openlocfilehash: 526f85ca4b8854a36232c75a55847a73a8d372cc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680302"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP ECC 'den veri kopyalama

Bu makalede, SAP Enterprise merkezi bileşeninden (ECC) veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Daha fazla bilgi için bkz. [kopyalama etkinliğine genel bakış](copy-activity-overview.md).

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için ayrıntılı giriş, comparme ve kılavuzla [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu SAP ECC Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

SAP ECC 'den, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu SAP ECC Bağlayıcısı şunları destekler:

- SAP NetWeaver sürüm 7,0 ve üzeri sürümlerde SAP ECC 'den veri kopyalama.
- SAP ECC OData Hizmetleri tarafından kullanıma sunulan nesnelerden verileri kopyalama, örneğin:

  - SAP tabloları veya görünümleri.
  - İş uygulaması programlama arabirimi [BAPı] nesneleri.
  - Veri ayıklayıcıları.
  - Bağlı bağdaştırıcılar aracılığıyla OData olarak alınabilecek SAP Işlem tümleştirmesine (PI) gönderilen veri veya ara belgeler (IDoc 'Lar).

- Temel kimlik doğrulaması kullanarak verileri kopyalama.

>[!TIP]
>SAP, SAP tablosu veya görünümü aracılığıyla SAP ECC 'den veri kopyalamak için, daha hızlı ve daha ölçeklenebilir olan [SAP tablosu](connector-sap-table.md) bağlayıcısını kullanın.

## <a name="prerequisites"></a>Ön koşullar

SAP ECC genellikle, SAP Gateway aracılığıyla OData Hizmetleri aracılığıyla varlıkları kullanıma sunar. Bu SAP ECC bağlayıcısını kullanmak için şunları yapmanız gerekir:

- **SAP Gateway ayarlayın**. 7,4 ' den sonraki SAP NetWeaver sürümleri olan sunucular için SAP Gateway zaten yüklüdür. Önceki sürümler için, OData Hizmetleri aracılığıyla SAP ECC verileri kullanıma sunmadan önce gömülü SAP Gateway veya SAP Gateway hub sistemini yüklemelisiniz. SAP Gateway ayarlamak için bkz. [Yükleme Kılavuzu](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **SAP OData hizmetini etkinleştirin ve yapılandırın**. Bir saniyede TCODE SıCF aracılığıyla OData hizmetini etkinleştirebilirsiniz. Ayrıca, hangi nesnelerin gösterilmesini gerektiğini de yapılandırabilirsiniz. Daha fazla bilgi için bkz. [adım adım Kılavuzu](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, SAP ECC bağlayıcısına özgü Data Factory varlıklarını tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

SAP ECC bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `type` | `type` özelliği `SapEcc`olarak ayarlanmalıdır. | Evet |
| `url` | SAP ECC OData hizmetinin URL 'SI. | Evet |
| `username` | SAP ECC 'ye bağlanmak için kullanılan Kullanıcı adı. | Hayır |
| `password` | SAP ECC 'ye bağlanmak için kullanılan düz metin parolası. | Hayır |
| `connectVia` | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Bir çalışma zamanı belirtmezseniz, varsayılan Azure tümleştirme çalışma zamanı kullanılır. | Hayır |

### <a name="example"></a>Örnek

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md). Aşağıdaki bölümde, SAP ECC veri kümesi tarafından desteklenen özelliklerin bir listesi verilmiştir.

SAP ECC 'den veri kopyalamak için veri kümesinin `type` özelliğini `SapEccResource`olarak ayarlayın.

Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `path` | SAP ECC OData varlığının yolu. | Evet |

### <a name="example"></a>Örnek

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). Aşağıdaki bölüm, SAP ECC kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sap-ecc-as-a-source"></a>Kaynak olarak SAP ECC

SAP ECC 'den veri kopyalamak için kopyalama etkinliğinin `source` bölümündeki `type` özelliğini `SapEccSource`olarak ayarlayın.

Kopyalama etkinliğinin `source` bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `type` | Kopyalama etkinliğinin `source` bölümünün `type` özelliği `SapEccSource`olarak ayarlanmalıdır. | Evet |
| `query` | Verileri filtrelemek için OData sorgu seçenekleri. Örneğin:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC Bağlayıcısı, verileri birleştirilmiş URL 'den kopyalar:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Daha fazla bilgi için bkz. [OData URL bileşenleri](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Hayır |

### <a name="example"></a>Örnek

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC için veri türü eşlemeleri

SAP ECC 'den veri kopyalarken aşağıdaki eşlemeler, SAP ECC verileri için OData veri türlerinden, geçici veri türlerini Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| OData veri türü | Data Factory geçici veri türü |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Karmaşık veri türleri şu anda desteklenmemektedir.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
