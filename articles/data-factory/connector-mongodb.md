---
title: MongoDB 'den veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak Mongo DB 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2021
ms.openlocfilehash: eae2d2adfe2cfdd7e47f2ace9ede9253003aa5bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368787"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory kullanarak MongoDB 'den veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, bir MongoDB veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!IMPORTANT]
>ADF, daha iyi yerel MongoDB desteği sağlayan MongoDB bağlayıcısının bu yeni sürümünü yayınlayın. Çözümünüzde önceki MongoDB bağlayıcısını, geriye dönük uyumluluk için olduğu gibi desteklenen bir şekilde kullanıyorsanız [MongoDB bağlayıcı (eski)](connector-mongodb-legacy.md) makalesine başvurun.


## <a name="supported-capabilities"></a>Desteklenen özellikler

MongoDB veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu MongoDB Bağlayıcısı **4,2 'e kadar olan sürümleri** destekler.


## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, MongoDB bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.


## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

MongoDB bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür |Type özelliği: **MongoDbV2** olarak ayarlanmalıdır |Yes |
| Dizisi |MongoDB bağlantı dizesini belirtin, örn. `mongodb://[username:password@]host[:port][/[database][?options]]` . Daha fazla ayrıntı için [bağlantı dizesinde MongoDB kılavuzuna](https://docs.mongodb.com/manual/reference/connection-string/) bakın. <br/><br /> Ayrıca, Azure Key Vault bir bağlantı dizesi de koyabilirsiniz. Daha ayrıntılı bilgi için [Azure Key Vault 'de mağaza kimlik bilgilerini](store-credentials-in-key-vault.md) inceleyin. |Yes |
| database | Erişmek istediğiniz veritabanının adı. | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

**Örnek:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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
| tür | DataSet 'in Type özelliği: **MongoDbV2Collection** olarak ayarlanmalıdır | Yes |
| Ma |MongoDB veritabanındaki koleksiyonun adı. |Yes |

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
| tür | Kopyalama etkinliği kaynağının Type özelliği: **MongoDbV2Source** olarak ayarlanmalıdır | Yes |
| filtre | Sorgu işleçlerini kullanarak seçim filtresini belirtir. Bir koleksiyondaki tüm belgeleri döndürmek için, bu parametreyi atlayın veya boş bir belge () geçirin {} . | No |
| cursorMethods. Project | Projeksiyon için belgelere döndürülecek alanları belirtir. Eşleşen belgelerdeki tüm alanları döndürmek için bu parametreyi atlayın. | No |
| cursorMethods. Sort | Sorgunun eşleşen belgeleri döndürdüğü sırayı belirtir. [İmleç. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)öğesine bakın. | No |
| cursorMethods. limit | Sunucunun döndürdüğü en fazla belge sayısını belirtir. [İmleç. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)öğesine bakın.  | No |
| cursorMethods. Skip | Atlanacak belge sayısını ve MongoDB 'nin sonuçları döndürmek için başladığı yeri belirtir. [İmleç. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)öğesine başvurun. | No |
| batchSize | MongoDB örneğinden alınan yanıtın her bir toplu işinde döndürülecek belge sayısını belirtir. Çoğu durumda, toplu iş boyutunu değiştirmek Kullanıcı veya uygulamayı etkilemez. Cosmos DB sınırlar, her toplu işlem, belge boyutu toplam değeri olan 40 MB 'ı aşamaz, bu nedenle belge boyutunuz büyük olduğunda bu değeri küçültün. | No<br/>(varsayılan değer **100**' dir) |

>[!TIP]
>ADF desteği BSON belgeyi **katı modda** kullanıyor. Filtre sorgunuzun kabuk modu yerine katı modda olduğundan emin olun. [MongoDB el ile](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)daha fazla açıklama bulabilirsiniz.

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
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
