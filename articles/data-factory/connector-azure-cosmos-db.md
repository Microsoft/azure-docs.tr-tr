---
title: Azure Cosmos DB (SQL API) Data Factory kullanarak verileri kopyalama ve dönüştürme
description: Azure Cosmos DB (SQL API) ve Data Factory kullanarak Azure Cosmos DB (SQL API) verilerini nasıl kopyalayacağınızı öğrenin.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 5e9db7c63e1493e1de5593262515040f071186e8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076807"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Cosmos DB (SQL API) Azure Data Factory kullanarak verileri kopyalama ve dönüştürme

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-azure-documentdb-connector.md)
> * [Geçerli sürüm](connector-azure-cosmos-db.md)

Bu makalede, verileri Azure Cosmos DB (SQL API) ' den kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir ve verileri Azure Cosmos DB (SQL API) dönüştürmek için veri akışını kullanabilirsiniz. Azure Data Factory hakkında bilgi edinmek için [giriş makalesi](introduction.md).

>[!NOTE]
>Bu bağlayıcı yalnızca SQL API 'sini Cosmos DB destekler. MongoDB API 'SI için, [MongoDB için Azure Cosmos DB API 'sine yönelik bağlayıcıya](connector-azure-cosmos-db-mongodb-api.md)başvurun. Diğer API türleri şu anda desteklenmiyor.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure Cosmos DB (SQL API) Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Kopyalama etkinliği için, bu Azure Cosmos DB (SQL API) Bağlayıcısı şunları destekler:

- Gelen ve Azure Cosmos DB veri kopyalama [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Azure Cosmos DB yazma **Ekle** veya **upsert**.
- İçeri aktarma ve JSON belgeleri olarak dışarı aktarma- ya da ya da tablolu bir veri kümesine veri kopyalayın. SQL veritabanı ve bir CSV dosyası verilebilir. Belgeleri JSON dosyalarından ya da başka bir Azure Cosmos DB koleksiyonuna veya farklı bir koleksiyon olarak kopyalamak için bkz. JSON belgelerini Içeri veya dışarı aktarma.

Veri Fabrikası ile tümleştirilir [Azure Cosmos DB toplu Yürütücü Kitaplığı](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) Azure Cosmos DB'ye yazdığınızda, en iyi performansı sağlamak için.

> [!TIP]
> [Veri geçişi video](https://youtu.be/5-SRNiC_qOU) Azure Cosmos DB için Azure Blob depolamadan veri kopyalama adımlarında size rehberlik yapacaktır. Video ayrıca genel olarak Azure Cosmos DB'ye veri almak için performans ayarlama konuları açıklanır.

## <a name="get-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Azure Cosmos DB (SQL API) özgü Data Factory varlıkları tanımlamak için kullanabileceğiniz özellikler hakkında ayrıntılı bilgiler sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri

Azure Cosmos DB (SQL API) bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | **Türü** özelliği ayarlanmalıdır **CosmosDb**. | Yes |
| connectionString |Azure Cosmos DB veritabanına bağlanmak için gereken bilgileri belirtin.<br />**Not**: veritabanı bilgisi bağlantı dizesinde aşağıdaki örneklerde gösterildiği gibi belirtmeniz gerekir. <br/>Bu alanı, Data Factory güvenli bir şekilde depolamak için SecureString olarak işaretleyin. Ayrıca hesap anahtarını Azure Key Vault yerleştirebilir ve `accountKey` yapılandırmasını bağlantı dizesinden dışarı çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . |Yes |
| connectVia | [Integration Runtime](concepts-integration-runtime.md) veri deposuna bağlanmak için kullanılacak. (Veri deponuz özel bir ağda yer alıyorsa) Azure Integration Runtime veya şirket içinde barındırılan tümleştirme çalışma zamanı kullanabilirsiniz. Bu özellik belirtilmezse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |Hayır |

**Örnek**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Azure Key Vault hesap anahtarını depolayın**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Bölümleri ve veri kümeleri tanımlamak için kullanılabilir olan özellikleri tam listesi için bkz: [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md).

Azure Cosmos DB (SQL API) veri kümesi için aşağıdaki özellikler desteklenir: 

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin **Type** özelliği **CosmosDbSqlApiCollection**olarak ayarlanmalıdır. |Yes |
| collectionName |Azure Cosmos DB belge koleksiyonu adı. |Yes |

"DocumentDbCollection" tür veri kümesini kullanıyorsanız, kopyalama ve arama etkinliği için geriye dönük uyumluluk için olduğu gibi hala desteklenir, veri akışı için desteklenmez. İleri doğru olan yeni modeli kullanmanız önerilir.

**Örnek**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Veri fabrikası tarafından şeması

Azure Cosmos DB gibi şemasız veri depoları için kopyalama etkinliği aşağıdaki listede açıklandığı şekilde şemayı algılar. İstediğiniz sürece [içeri veya dışarı aktarma JSON belgeleri olarak-olan](#import-or-export-json-documents), veri yapısını belirlemek için en iyi uygulamadır **yapısı** bölümü.

Data Factory, etkinlikte belirttiğiniz eşlemeyi de alır. Bir satır bir sütun için bir değer içermiyorsa, bir null değer sütun değeri için sağlanır.

Bir eşleme belirtmezseniz, Data Factory hizmeti verilerin ilk satırını kullanarak şemayı algılar. İlk satır tam şemayı içermiyorsa, etkinlik işleminin sonucunda bazı sütunlar eksik olur.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Bu bölüm Azure Cosmos DB (SQL API) kaynak ve havuz desteğinin özelliklerinin bir listesini sağlar.

Bölümleri ve etkinlikleri tanımlamak için kullanılabilir olan özellikleri tam listesi için bkz: [işlem hatları](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Kaynak olarak Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) ' den veri kopyalamak için kopyalama etkinliğindeki **kaynak** türünü **Documentdbcollectionsource**olarak ayarlayın. 

Kopyalama etkinliği aşağıdaki özellikler desteklenir **kaynak** bölümü:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının **Type** özelliği **Cosmosdbsqlapısource**olarak ayarlanmalıdır. |Yes |
| sorgu |Verileri okumak için Azure Cosmos DB sorgusu belirtin.<br/><br/>Örnek:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Hayır <br/><br/>Belirtilmemişse, bu SQL deyimi yürütülür: `select <columns defined in structure> from mycollection` |
| Preferredregion | Cosmos DB verileri alırken Bağlanılacak bölgelerin tercih edilen listesi. | Hayır |
| pageSize | Sorgu sonucunun sayfa başına belge sayısı. Varsayılan değer olan "-1", hizmet tarafı dinamik sayfa boyutunu 1000 'e kadar kullanır. | Hayır |

"DocumentDbCollectionSource" tür kaynağını kullanırsanız, geriye dönük uyumluluk için olduğu gibi hala desteklenir. Cosmos DB verileri kopyalamak için daha zengin yetenekler sağlayan yeni modeli ileri kullanmanız önerilir.

**Örnek**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Havuz olarak Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) ' e veri kopyalamak için kopyalama etkinliğindeki **Havuz** türünü **Documentdbcollectionsink**olarak ayarlayın. 

Kopyalama etkinliği aşağıdaki özellikler desteklenir **kaynak** bölümü:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuzunun **Type** özelliği **Cosmosdbsqlapisink**olarak ayarlanmalıdır. |Yes |
| writeBehavior |Azure Cosmos DB'ye veri yazmak açıklar. İzin verilen değerler: **Ekle** ve **upsert**.<br/><br/>Davranışını **upsert** aynı Kimliğe sahip bir belge zaten varsa belge değiştirmek üzere; Aksi takdirde, belge ekleyin.<br /><br />**Not**: Data Factory bir kimliği, özgün belgenin veya sütun eşlemesi tarafından belirtilmezse bir belge için bir kimliği otomatik olarak oluşturur. İçin emin olmanız gerekir, yani **upsert** beklendiği şekilde çalışması için belgeyi bir kimliği vardır. |Hayır<br />(varsayılan değer **Ekle**) |
| writeBatchSize | Veri fabrikasının kullandığı [Azure Cosmos DB toplu Yürütücü Kitaplığı](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) Azure Cosmos DB'ye veri yazmak için. **Writebatchsize** ÖZELLIĞI, ADF 'nin kitaplığa sağladığı belge boyutunu denetler. Değerini artırmayı deneyin **writeBatchSize** performansı ve değeri, azalan artırmak için belgenizin durdurulmasını büyük boyut - ipuçlarına bakın. |Hayır<br />(varsayılan değer **10.000**) |
| disableMetricsCollection | Data Factory, performansı iyileştirmek ve önerileri kopyalamak için Cosmos DB ru gibi ölçümleri toplar. Bu davranışla ilgileniyorlarsa, kapatmak için `true` belirtin. | Hayır (varsayılan `false`) |

>[!TIP]
>Cosmos DB tek isteğin boyutunu 2 MB ile sınırlar. İstek boyutu formüldür tek belge boyutu = * yazma toplu iş boyutu. Hata bildiren ulaşırsanız **"istek boyutu çok büyük."** , **azaltmak `writeBatchSize` değer** kopyalama havuz yapılandırması.

"DocumentDbCollectionSink" tür kaynağını kullanırsanız, geriye dönük uyumluluk için olduğu gibi hala desteklenir. Cosmos DB verileri kopyalamak için daha zengin yetenekler sağlayan yeni modeli ileri kullanmanız önerilir.

**Örnek**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki [kaynak dönüşümden](data-flow-source.md) ve [Havuz dönüşümünde](data-flow-sink.md) ayrıntıları öğrenin.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="import-or-export-json-documents"></a>JSON belgelerini içeri veya dışarı aktarma

Bu Azure Cosmos DB (SQL API) bağlayıcısını kolayca kullanabilirsiniz:

* JSON belgeleri için Azure Cosmos DB, Azure Blob Depolama, Azure Data Lake Store ve Azure Data Factory destekleyen diğer dosya tabanlı depoları da dahil olmak üzere çeşitli kaynaklardan içeri aktarın.
* JSON belgeleri bir Azure Cosmos DB koleksiyonundan çeşitli dosya tabanlı depoları dışarı aktarın.
* Belgeler iki Azure Cosmos DB koleksiyonları arasında kopyalama-olduğu.

Şemadan kopyalama elde etmek için:

* Veri kopyalama aracını kullandığınızda, seçin **Dışarı Aktar-JSON dosyaları veya Cosmos DB koleksiyonu** seçeneği.
* Etkinlik yazma kullandığınızda, kaynak veya havuz için karşılık gelen dosya deposuyla JSON biçimi ' ni seçin.

## <a name="next-steps"></a>Sonraki adımlar

Kopyalama etkinliği kaynak olarak destekler ve şu havuzlar Azure Data Factory'de veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
