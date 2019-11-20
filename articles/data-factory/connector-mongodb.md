---
title: Azure Data Factory kullanarak MongoDB 'den veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak Mongo DB 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2f675bed803873e46ee25ca7bc0afda5cb09c07b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680655"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory kullanarak MongoDB 'den veri kopyalama

Bu makalede, bir MongoDB veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!IMPORTANT]
>ADF, daha iyi yerel MongoDB desteği sağlayan MongoDB bağlayıcısının bu yeni sürümünü yayınlayın. Çözümünüzde önceki MongoDB bağlayıcısını, geriye dönük uyumluluk için olduğu gibi desteklenen bir şekilde kullanıyorsanız [MongoDB bağlayıcı (eski)](connector-mongodb-legacy.md) makalesine başvurun.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

MongoDB veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu MongoDB Bağlayıcısı **3,4 'e kadar olan sürümleri**destekler.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, MongoDB bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

MongoDB bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type |Type özelliği: **MongoDbV2** olarak ayarlanmalıdır |Evet |
| connectionString |`mongodb://[username:password@]host[:port][/[database][?options]]`MongoDB bağlantı dizesini belirtin. Daha fazla ayrıntı için [bağlantı dizesinde MongoDB kılavuzuna](https://docs.mongodb.com/manual/reference/connection-string/) bakın. <br/><br />Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** türü olarak işaretleyin. Ayrıca, [Azure Key Vault depolanan bir gizli](store-credentials-in-key-vault.md)dizi için de başvurabilirsiniz. |Evet |
| veritabanı | Erişmek istediğiniz veritabanının adı. | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

**Örnek:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md). MongoDB veri kümesi için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **MongoDbV2Collection** olarak ayarlanmalıdır | Evet |
| Ma |MongoDB veritabanındaki koleksiyonun adı. |Evet |

**Örnek:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, MongoDB kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="mongodb-as-source"></a>Kaynak olarak MongoDB

Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **MongoDbV2Source** olarak ayarlanmalıdır | Evet |
| filtreyle | Sorgu işleçlerini kullanarak seçim filtresini belirtir. Bir koleksiyondaki tüm belgeleri döndürmek için, bu parametreyi atlayın veya boş bir belge ({}) geçirin. | Hayır |
| cursorMethods. Project | Projeksiyon için belgelere döndürülecek alanları belirtir. Eşleşen belgelerdeki tüm alanları döndürmek için bu parametreyi atlayın. | Hayır |
| cursorMethods. Sort | Sorgunun eşleşen belgeleri döndürdüğü sırayı belirtir. [İmleç. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)öğesine bakın. | Hayır |
| cursorMethods. limit | Sunucunun döndürdüğü en fazla belge sayısını belirtir. [İmleç. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)öğesine bakın.  | Hayır |
| cursorMethods. Skip | Atlanacak belge sayısını ve MongoDB 'nin sonuçları döndürmek için başladığı yeri belirtir. [İmleç. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)öğesine başvurun. | Hayır |
| batchSize | MongoDB örneğinden alınan yanıtın her bir toplu işinde döndürülecek belge sayısını belirtir. Çoğu durumda, toplu iş boyutunu değiştirmek Kullanıcı veya uygulamayı etkilemez. Cosmos DB sınırlar, her toplu işlem, belge boyutunun batchSize toplam sayısı olan 40MB 'ı aşamaz, bu nedenle belge boyutunuz büyükse bu değeri azaltın. | Hayır<br/>(varsayılan değer **100**' dir) |

>[!TIP]
>ADF desteği BSON belgeyi **katı modda**kullanıyor. Filtre sorgunuzun kabuk modu yerine katı modda olduğundan emin olun. [MongoDB el ile](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)daha fazla açıklama bulabilirsiniz.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>JSON belgelerini olduğu gibi dışarı aktarma

Bu MongoDB bağlayıcısını, JSON belgelerini bir MongoDB koleksiyonundan farklı dosya tabanlı depolara veya Azure Cosmos DB dışarı aktarmak için kullanabilirsiniz. Bu tür şemadan bağımsız bir kopya elde etmek için, kopyalama etkinliğinde veri kümesi ve şema eşleme ' de "yapı" (kısaca *şema*) bölümünü atlayın.

## <a name="schema-mapping"></a>Şema eşleme

MongoDB 'den tablolu havuza veri kopyalamak için [Şema eşlemesi](copy-activity-schema-and-type-mapping.md#schema-mapping)' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
