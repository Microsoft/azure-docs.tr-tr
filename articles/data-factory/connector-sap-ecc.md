---
title: SAP ECC 'den veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak SAP ECC 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: a3e701f3d433b5b52d8992035ac4ad75b78cb795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386705"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP ECC 'den veri kopyalama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, SAP Enterprise merkezi bileşeninden (ECC) veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Daha fazla bilgi için bkz. [kopyalama etkinliğine genel bakış](copy-activity-overview.md).

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için, her SAP Bağlayıcısı, karşılaştırma ve kılavuza ayrıntılı giriş ile [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen özellikler

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

Sürüm 7,0 veya üzeri, SAP ECC sürümü yerine SAP NetWeaver sürümüne başvurur. Örneğin, SAP ECC 6,0 EHP 7, genel olarak NetWeaver sürüm >= 7,4 ' dir. Ortamınız hakkında emin değilseniz SAP sisteminizden sürümü onaylamaya yönelik adımlar aşağıda verilmiştir:

1. SAP sistemine bağlanmak için SAP GUI 'yi kullanın. 
2. **Sistem**  ->  **durumuna** gidin. 
3. SAP_BASIS sürümünü denetleyin, 701 ' den büyük veya ona eşit olduğundan emin olun.  
      ![SAP_BASIS denetle](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>SAP, SAP tablosu veya görünümü aracılığıyla SAP ECC 'den veri kopyalamak için, daha hızlı ve daha ölçeklenebilir olan [SAP tablosu](connector-sap-table.md) bağlayıcısını kullanın.

## <a name="prerequisites"></a>Önkoşullar

Bu SAP ECC bağlayıcısını kullanmak için SAP Gateway aracılığıyla SAP ECC varlıklarını OData Hizmetleri aracılığıyla kullanıma sunabilirsiniz. Daha ayrıntılı belirtmek gerekirse:

- **SAP Gateway ayarlayın**. 7,4 ' den sonraki SAP NetWeaver sürümleri olan sunucular için SAP Gateway zaten yüklüdür. Önceki sürümler için, OData Hizmetleri aracılığıyla SAP ECC verileri kullanıma sunmadan önce gömülü SAP Gateway veya SAP Gateway hub sistemini yüklemelisiniz. SAP Gateway ayarlamak için bkz. [Yükleme Kılavuzu](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **SAP OData hizmetini etkinleştirin ve yapılandırın**. Bir saniyede TCODE SıCF aracılığıyla OData hizmetini etkinleştirebilirsiniz. Ayrıca, hangi nesnelerin gösterilmesini gerektiğini de yapılandırabilirsiniz. Daha fazla bilgi için bkz. [adım adım Kılavuzu](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, SAP ECC bağlayıcısına özgü Data Factory varlıklarını tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

SAP ECC bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `type` | `type`Özelliğin olarak ayarlanması gerekir `SapEcc` . | Yes |
| `url` | SAP ECC OData hizmetinin URL 'SI. | Yes |
| `username` | SAP ECC 'ye bağlanmak için kullanılan Kullanıcı adı. | No |
| `password` | SAP ECC 'ye bağlanmak için kullanılan düz metin parolası. | No |
| `connectVia` | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Bir çalışma zamanı belirtmezseniz, varsayılan Azure tümleştirme çalışma zamanı kullanılır. | No |

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

SAP ECC 'den veri kopyalamak için veri `type` kümesinin özelliğini olarak ayarlayın `SapEccResource` .

Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `path` | SAP ECC OData varlığının yolu. | Yes |

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

SAP ECC 'den veri kopyalamak için `type` `source` kopyalama etkinliğinin bölümündeki özelliğini olarak ayarlayın `SapEccSource` .

Kopyalama etkinliğinin bölümünde aşağıdaki özellikler desteklenir `source` :

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| `type` | `type`Kopyalama etkinliğinin `source` bölümünün özelliği olarak ayarlanmalıdır `SapEccSource` . | Yes |
| `query` | Verileri filtrelemek için OData sorgu seçenekleri. Örnek:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC Bağlayıcısı, verileri birleştirilmiş URL 'den kopyalar:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Daha fazla bilgi için bkz. [OData URL bileşenleri](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |
| `sapDataColumnDelimiter` | Çıktı verilerini ayırmak için SAP RFC 'ye geçirilen sınırlayıcı olarak kullanılan tek karakter. | No |
| `httpRequestTimeout` | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Bu değer, yanıt verilerinin okunması için zaman aşımı değil, yanıt almaya yönelik zaman aşımı değeridir. Belirtilmemişse, varsayılan değer **00:30:00** ' dir (30 dakika). | No |

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
