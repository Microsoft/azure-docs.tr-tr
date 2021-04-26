---
title: Azure Cosmos DB verileri kopyalama ve dönüştürme (SQL API)
description: Azure Cosmos DB (SQL API) ve Data Factory kullanarak Azure Cosmos DB (SQL API) verilerini nasıl kopyalayacağınızı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: d42f30ebd72dca81255ddc02a9440db19979536d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608079"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory'yi kullanarak Azure Cosmos DB'deki (SQL API) verileri kopyalama ve dönüştürme

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-azure-documentdb-connector.md)
> * [Güncel sürüm](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede Azure Cosmos DB’den (SQL API) ve Azure Cosmos DB'ye veri kopyalamak için Azure Data Factory’nin Kopya Etkinliğini kullanma ve Azure Cosmos DB’deki (SQL API) verileri dönüştürmek için Veri Akışı’nı kullanma işlemleri açıklanır. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.



>[!NOTE]
>Bu bağlayıcı yalnızca SQL API 'sini Cosmos DB destekler. MongoDB API 'SI için, [MongoDB için Azure Cosmos DB API 'sine yönelik bağlayıcıya](connector-azure-cosmos-db-mongodb-api.md)başvurun. Diğer API türleri şu anda desteklenmiyor.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure Cosmos DB (SQL API) Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Kopyalama etkinliği için, bu Azure Cosmos DB (SQL API) Bağlayıcısı şunları destekler:

- Ve Azure Cosmos DB [SQL API](../cosmos-db/introduction.md)'sine verileri kopyalayın.
- **Insert** veya **upsert** olarak Azure Cosmos DB yazın.
- JSON belgelerini olduğu gibi içeri aktarın veya dışarı aktarın ya da tablo veri kümesindeki verileri veya veri kümesini kopyalayın. Örnek olarak bir SQL veritabanı ve bir CSV dosyası bulunur. Belgeleri JSON dosyalarından veya ya da başka bir Azure Cosmos DB koleksiyonuna olduğu gibi kopyalamak için bkz. [JSON belgelerini içeri ve dışarı aktarma](#import-and-export-json-documents).

Data Factory, Azure Cosmos DB yazarken en iyi performansı sağlamak için [Azure Cosmos DB toplu yürütücü kitaplığıyla](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) tümleştirilir.

> [!TIP]
> [Veri geçiş videosu](https://youtu.be/5-SRNiC_qOU) , verileri Azure Blob depolamadan Azure Cosmos DB kopyalama adımlarında size yol gösterir. Video Ayrıca verileri genel olarak Azure Cosmos DB almak için performans ayarlama konularını açıklar.

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Azure Cosmos DB (SQL API) özgü Data Factory varlıkları tanımlamak için kullanabileceğiniz özellikler hakkında ayrıntılı bilgiler sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Azure Cosmos DB (SQL API) bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | **Type** özelliği **cosmosdb** olarak ayarlanmalıdır. | Yes |
| Dizisi |Azure Cosmos DB veritabanına bağlanmak için gereken bilgileri belirtin.<br />**Note**: bağlantı dizesinde, izleyen örneklerde gösterildiği gibi veritabanı bilgilerini belirtmeniz gerekir. <br/> Ayrıca hesap anahtarını Azure Key Vault yerleştirebilir ve `accountKey` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . |Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan tümleştirme çalışma zamanını (veri depolubir özel ağda bulunuyorsa) kullanabilirsiniz. Bu özellik belirtilmezse, varsayılan Azure Integration Runtime kullanılır. |No |

**Örnek**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
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

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md).

Azure Cosmos DB (SQL API) veri kümesi için aşağıdaki özellikler desteklenir: 

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin **Type** özelliği **CosmosDbSqlApiCollection** olarak ayarlanmalıdır. |Yes |
| Ma |Azure Cosmos DB belge koleksiyonunun adı. |Yes |

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

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Bu bölüm Azure Cosmos DB (SQL API) kaynak ve havuz desteğinin özelliklerinin bir listesini sağlar. Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Kaynak olarak Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) ' den veri kopyalamak için kopyalama etkinliğindeki **kaynak** türünü **Documentdbcollectionsource** olarak ayarlayın. 

Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının **Type** özelliği **Cosmosdbsqlapısource** olarak ayarlanmalıdır. |Yes |
| sorgu |Verileri okumak için Azure Cosmos DB sorgusunu belirtin.<br/><br/>Örnek:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Belirtilmemişse, bu SQL deyimi yürütülür: `select <columns defined in structure> from mycollection` |
| Preferredregion | Cosmos DB veri alınırken Bağlanılacak bölgelerin tercih edilen listesi. | No |
| pageSize | Sorgu sonucunun sayfa başına belge sayısı. Varsayılan değer olan "-1", hizmet tarafı dinamik sayfa boyutunu 1000 'e kadar kullanır. | No |
| detectDatetime | Belgelerdeki dize değerlerinden tarih saat değerinin algılanmayacağını belirtir. İzin verilen değerler: **true** (varsayılan), **false**. | No |

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

Cosmos DB verileri kopyalama sırasında [JSON belgelerini olduğu gibi dışarı aktarmak](#import-and-export-json-documents)istemiyorsanız en iyi yöntem kopyalama etkinliğinde eşlemeyi belirtmektir. Data Factory, etkinlik üzerinde belirttiğiniz eşlemeyi alır. bir satır bir sütun için değer içermiyorsa, sütun değeri için null değer sağlanır. Eşleme belirtmezseniz, verileri verilerdeki ilk satırı kullanarak şemayı Data Factory. İlk satır tam şemayı içermiyorsa, etkinlik işleminin sonucunda bazı sütunlar eksik olur.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Havuz olarak Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) ' e veri kopyalamak için kopyalama etkinliğindeki **Havuz** türünü **Documentdbcollectionsink** olarak ayarlayın. 

Aşağıdaki özellikler, kopyalama etkinliği **havuzu** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun **Type** özelliği **Cosmosdbsqlapisink** olarak ayarlanmalıdır. |Yes |
| writeBehavior |Azure Cosmos DB verilerin nasıl yazılacağını açıklar. İzin verilen değerler: **Insert** ve **upsert**.<br/><br/>Aynı KIMLIĞE sahip bir belge zaten mevcutsa, **upsert** 'un davranışı belgeyi değiştirir; Aksi takdirde, belgeyi ekleyin.<br /><br />**Note**: özgün belgede veya sütun EŞLEMESINDE bir kimlik belirtilmemişse, otomatik olarak BIR belge kimliği üretir Data Factory. Yani, **büyük** bir şekilde çalışması için BELGENIZDE bir kimliğe sahip olduğundan emin olmanız gerekir. |No<br />(varsayılan değer **Insert**'dir) |
| writeBatchSize | Data Factory, Azure Cosmos DB verileri yazmak için [Azure Cosmos DB toplu yürütücü kitaplığını](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) kullanır. **Writebatchsize** ÖZELLIĞI, ADF 'nin kitaplığa sağladığı belge boyutunu denetler. Performansı artırmak ve belge boyutunuz büyük olduğunda bu değeri azaltmak için **Writebatchsize** değerini artırmayı deneyebilirsiniz. |No<br />(varsayılan değer **10.000**' dir) |
| disableMetricsCollection | Data Factory, performansı iyileştirmek ve önerileri kopyalamak için Cosmos DB ru gibi ölçümleri toplar. Bu davranışla ilgileniyorlarsa, `true` devre dışı bırakmak için belirtin. | Hayır (varsayılan değer `false` ) |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |


>[!TIP]
>JSON belgelerini olduğu gibi içeri aktarmak için [JSON belgelerini içeri veya dışarı aktarma](#import-and-export-json-documents) bölümüne bakın; tablosal şekillendirilmiş verilerden kopyalamak için, [ilişkisel veritabanından Cosmos DB geçirme](#migrate-from-relational-database-to-cosmos-db)bölümüne bakın.

>[!TIP]
>Cosmos DB tek isteğin boyutunu 2MB olarak sınırlandırır. Formül, Istek boyutu = tek belge boyutu * yazma toplu Iş boyutu ' dür. **"İstek boyutu çok büyük"** hatası ile karşılaşırsanız, kopyalama havuzu yapılandırmasındaki **`writeBatchSize` değeri azaltın** .

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

### <a name="schema-mapping"></a>Şema eşleme

Verileri Azure Cosmos DB tablo havuzuna veya ters çevrilme göre kopyalamak için [Şema eşlemesi](copy-activity-schema-and-type-mapping.md#schema-mapping)' ne bakın.

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki verileri dönüştürürken Cosmos DB koleksiyonları okuyabilir ve yazabilirsiniz. Daha fazla bilgi için bkz. veri akışlarını eşleme içindeki [kaynak dönüştürme](data-flow-source.md) ve [Havuz dönüşümü](data-flow-sink.md) .

### <a name="source-transformation"></a>Kaynak dönüştürme

Azure Cosmos DB özgü ayarlar, kaynak dönüşümünün **kaynak seçenekleri** sekmesinde bulunabilir. 

**Sistem sütunlarını dahil et:** Doğru, ```id``` , ```_ts``` ve diğer sistem sütunları cosmosdb 'den veri akışı meta verilerinize dahil edilir. Koleksiyonları güncelleştirirken, mevcut satır kimliğini elde edebilmeniz için bunu dahil etmek önemlidir.

**Sayfa boyutu:** Sorgu sonucunun sayfa başına belge sayısı. Varsayılan değer, hizmet dinamik sayfasını 1000 ' e kadar kullanan "-1" ' dir.

**Aktarım hızı:** Bu veri akışının her yürütmesi için, okuma işlemi sırasında CosmosDB koleksiyonunuza uygulamak istediğiniz ru sayısı için isteğe bağlı bir değer ayarlayın. Minimum değer 400 ' dir.

**Tercih edilen bölgeler:** Bu işlem için tercih edilen okuma bölgelerini seçin.

#### <a name="json-settings"></a>JSON Ayarları

**Tek belge:** ADF dosyanın tamamını tek bir JSON belgesi olarak değerlendirmek istiyorsanız bu seçeneği belirleyin.

**Tırnak Işaretleri olmayan sütun adları:** JSON 'daki sütun adları tırnak içine alınmadığı takdirde bu seçeneği belirleyin.

**Açıklamalar içeriyor:** JSON belgelerinizin verilerde açıklama varsa bu seçimi kullanın.

**Tek tırnak:** Belgenizdeki sütunlar ve değerler tek tırnak ile alıntılandıysanız bu seçilmelidir.

**Ters eğik çizgi kaçışı:** JSON 'inizdeki karakterleri kaçış için ters eğik çizgi kullanıyorsanız bu seçeneği belirleyin.

### <a name="sink-transformation"></a>Havuz dönüştürme

Azure Cosmos DB özgü ayarlar, havuz dönüşümünün **Ayarlar** sekmesinde bulunabilir.

**Güncelleştirme yöntemi:** Veritabanı Hedefinizdeki hangi işlemlere izin verileceğini belirler. Varsayılan değer yalnızca eklemeleri izin verir. Satırları güncelleştirmek, kaldırmak veya silmek için, bu eylemler için satırları etiketlemek üzere alter-Row dönüşümü gereklidir. Güncelleştirmeler, yukarı ve silme için bir anahtar sütunu veya sütunları ayarlanacak satırı belirleyecek şekilde ayarlanmalıdır.

**Koleksiyon eylemi:** Yazmadan önce hedef koleksiyonun yeniden oluşturulup oluşturulmayacağını belirler.
* Hiçbiri: koleksiyona hiçbir eylem yapılmaz.
* Yeniden oluştur: koleksiyon bırakılır ve yeniden oluşturulur

**Toplu iş boyutu**: her toplu işte Cosmos DB koleksiyon için kaç nesne yazıldığını temsil eden bir tamsayı. Genellikle, varsayılan toplu iş boyutundan itibaren yeterlidir. Bu değeri daha fazla ayarlamak için, bkz:

- Cosmos DB tek isteğin boyutunu 2MB olarak sınırlandırır. Formül "Istek boyutu = tek belge boyutu * toplu Iş boyutu" dir. "Istek boyutu çok büyük" hatasıyla karşılaşırsanız, toplu iş boyutu değerini azaltın.
- Yığın boyutunun daha büyük olması, ADF 'nin elde daha iyi hale geldiğinden, iş yükünüzü güçlendirin.

**Bölüm anahtarı:** Koleksiyonunuz için bölüm anahtarını temsil eden bir dize girin. Örnek: ```/movies/title```

**Aktarım hızı:** Bu veri akışının her yürütmesi için CosmosDB koleksiyonunuza uygulamak istediğiniz ru sayısı için isteğe bağlı bir değer ayarlayın. Minimum değer 400 ' dir.

**Yazma aktarım hızı bütçesi:** Bu veri akışı yazma işlemi için ayırmak istediğiniz ru 'ları temsil eden, koleksiyona ayrılan toplam verimlilik dışında bir tamsayı.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="import-and-export-json-documents"></a>JSON belgelerini içeri ve dışarı aktarma

Bu Azure Cosmos DB (SQL API) bağlayıcısını kolayca kullanabilirsiniz:

* İki Azure Cosmos DB koleksiyon arasında belge kopyalama.
* Azure Blob depolama, Azure Data Lake Store ve Azure Data Factory desteklediği diğer dosya tabanlı depolardan da dahil olmak üzere çeşitli kaynaklardan gelen JSON belgelerini Azure Cosmos DB içeri aktarın.
* JSON belgelerini bir Azure Cosmos DB koleksiyonundan çeşitli dosya tabanlı depolarına dışarı aktarın.

Şema belirsiz kopya elde etmek için:

* Veri Kopyalama aracını kullanırken, **-as JSON dosyalarını veya Cosmos DB koleksiyon** seçeneğini belirleyin.
* Etkinlik yazma kullandığınızda, kaynak veya havuz için karşılık gelen dosya deposuyla JSON biçimi ' ni seçin.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>İlişkisel veritabanından Cosmos DB geçirin

İlişkisel bir veritabanından geçiş yaparken (örneğin, Azure Cosmos DB SQL Server, kopyalama etkinliği kaynaktaki tablo verilerini, Cosmos DB JSON belgelerini düzleştirmek üzere kolayca bağlanabilir. Bazı durumlarda, verileri bir JSON belgesinde ilgili tüm alt öğeleri katıştırarak verileri yeniden kullanmak gibi [Azure Cosmos DB veri modellemeye](../cosmos-db/modeling-data.md)göre NoSQL kullanım örnekleri için en uygun şekilde, veri modelini yeniden tasarlamanız gerekebilir. Böyle bir durumda, [Bu makaleye](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) Azure Data Factory kopyalama etkinliğini kullanarak nasıl elde leyeceğinizi gösteren bir yönergeye başvurun.

## <a name="next-steps"></a>Sonraki adımlar

Kopyalama etkinliğinin Azure Data Factory kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).