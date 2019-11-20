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
ms.openlocfilehash: 0c2c2d9ad78bb09a37faaa5825f8dae3e27370ea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680684"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory kullanarak MongoDB 'den veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Geçerli sürüm](connector-mongodb.md)

Bu makalede, bir MongoDB veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!IMPORTANT]
>ADF yayını bu ODBC tabanlı uygulamayla kıyaslanarak daha iyi yerel MongoDB desteği sağlayan yeni bir MongoDB Bağlayıcısı, Ayrıntılar için [MongoDB bağlayıcı](connector-mongodb.md) makalesine başvurun. Bu eski MongoDB Bağlayıcısı, yeni iş yükleri için olduğu gibi, geriye dönük olarak olduğu gibi desteklenir. lütfen yeni bağlayıcıyı kullanın.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

MongoDB veritabanından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu MongoDB Bağlayıcısı şunları destekler:

- MongoDB **sürümleri 2,4, 2,6, 3,0, 3,2, 3,4 ve 3,6**.
- **Temel** veya **anonim** kimlik doğrulaması kullanarak verileri kopyalama.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime, yerleşik bir MongoDB sürücüsü sağlar, bu nedenle MongoDB 'den veri kopyalarken herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, MongoDB bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

MongoDB bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type |Type özelliği: **MongoDB** olarak ayarlanmalıdır |Evet |
| sunucu |MongoDB sunucusunun IP adresi veya ana bilgisayar adı. |Evet |
| port |MongoDB sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. |Hayır (varsayılan değer 27017) |
| Dosyasında |Erişmek istediğiniz MongoDB veritabanının adı. |Evet |
| authenticationType | MongoDB veritabanına bağlanmak için kullanılan kimlik doğrulaması türü.<br/>İzin verilen değerler: **Basic**ve **Anonymous**. |Evet |
| kullanıcı adı |MongoDB 'ye erişmek için Kullanıcı hesabı. |Evet (temel kimlik doğrulaması kullanılıyorsa). |
| password |Kullanıcının parolası. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Evet (temel kimlik doğrulaması kullanılıyorsa). |
| authSource |Kimlik doğrulaması için kimlik bilgilerinizi denetlemek üzere kullanmak istediğiniz MongoDB veritabanının adı. |Hayır. Temel kimlik doğrulaması için varsayılan olarak yönetici hesabı ve databaseName özelliği kullanılarak belirtilen veritabanı kullanılır. |
| enableSsl | Sunucu bağlantılarının SSL kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer false'tur.  | Hayır |
| allowSelfSignedServerCert | Sunucudan kendinden imzalı sertifikalara izin verilip verilmeyeceğini belirtir. Varsayılan değer false'tur.  | Hayır |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

**Örnek:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md). MongoDB veri kümesi için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **Mongodbcollection** olarak ayarlanmalıdır | Evet |
| Ma |MongoDB veritabanındaki koleksiyonun adı. |Evet |

**Örnek:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
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
| type | Kopyalama etkinliği kaynağının Type özelliği: **Mongodbsource** olarak ayarlanmalıdır | Evet |
| sorgu |Verileri okumak için özel SQL-92 sorgusunu kullanın. Örneğin: select * from MyTable. |Hayır (veri kümesinde "collectionName" belirtilmişse) |

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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> SQL sorgusunu belirttiğinizde, tarih saat biçimine dikkat edin. Örneğin: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` veya parametresini kullanmak için `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Data Factory şema

Azure Data Factory hizmet, koleksiyondaki **en son 100 belgeyi** kullanarak bir MongoDB koleksiyonundan şemayı hallederler. Bu 100 belgeler tam şema içermiyorsa, kopyalama işlemi sırasında bazı sütunlar yoksayılabilir.

## <a name="data-type-mapping-for-mongodb"></a>MongoDB için veri türü eşlemesi

MongoDB 'den veri kopyalarken aşağıdaki eşlemeler MongoDB veri türlerinden, geçici veri türlerini Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| MongoDB veri türü | Veri Fabrikası geçici veri türü |
|:--- |:--- |
| ý |Byte [] |
| Boole |Boole |
| Tarih |DateTime |
| NumberDouble |Çift |
| Numberınt |Int32 |
| NumberLong |Int64 |
| Uzantının |Dize |
| Dize |Dize |
| EDIN |Guid |
| Nesne |İç içe ayırıcı olarak "_" ile sütunları düzleştirmek için yeniden Renklendir |

> [!NOTE]
> Sanal tabloları kullanarak diziler için destek hakkında bilgi edinmek için [sanal tabloları kullanarak karmaşık türler Için destek](#support-for-complex-types-using-virtual-tables) bölümüne bakın.
>
> Şu anda şu MongoDB veri türleri desteklenmez: DBPointer, JavaScript, Max/Min anahtarı, normal Ifade, simge, zaman damgası, tanımsız.

## <a name="support-for-complex-types-using-virtual-tables"></a>Sanal tabloları kullanarak karmaşık türler için destek

Azure Data Factory, MongoDB veritabanınıza bağlanmak ve veri kopyalamak için yerleşik bir ODBC sürücüsü kullanır. Belge genelinde farklı türlere sahip diziler veya nesneler gibi karmaşık türler için, sürücü verileri ilgili sanal tablolara yeniden normalleştirir. Özellikle, bir tablo bu tür sütunlar içeriyorsa, sürücü aşağıdaki sanal tabloları oluşturur:

* Karmaşık tür sütunları hariç gerçek tabloyla aynı verileri içeren bir **temel tablo**. Temel tablo, temsil ettiği gerçek tabloyla aynı adı kullanır.
* İç içe geçmiş verileri genişleten her karmaşık tür sütunu için bir **sanal tablo** . Sanal tablolar, gerçek tablonun adı, bir ayırıcı "_" ve dizi ya da nesnenin adı kullanılarak adlandırılır.

Sanal tablolar, gerçek tablodaki verilere başvurur ve bu da sürücünün, verilerin yoğun verilere erişmesine olanak tanır. Sanal tabloları sorgulama ve birleştirme yoluyla MongoDB dizilerinin içeriğine erişebilirsiniz.

### <a name="example"></a>Örnek

Örneğin, buradaki ExampleTable, her hücrede bir nesne dizisi olan bir sütun içeren bir MongoDB tablosu ve bir sütun skaler türler dizisi olan bir sütundan oluşur.

| _kimlik | Müşteri adı | Faturalar | Hizmet Düzeyi | Lendir |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", öğe: "Toaster", Price: "456", indirim: "0.2"}, {invoice_id: "124", öğe: "oven", Fiyat: "1235", indirim: "0,2"}] |Silver |[5, 6] |
| 2222 |V |[{invoice_id: "135", öğe: "Fridge", Fiyat: "12543", indirim: "0,0"}] |Gold |[1, 2] |

Bu tek tabloyu temsil eden sürücü birden çok sanal tablo oluşturur. İlk sanal tablo, örnekte gösterilen "ExampleTable" adlı temel tablodur. Temel tablo özgün tablonun tüm verilerini içerir, ancak dizilerdeki veriler atlanmıştır ve sanal tablolarda genişletilir.

| _kimlik | Müşteri adı | Hizmet Düzeyi |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |V |Gold |

Aşağıdaki tablolarda, örnekteki özgün dizileri temsil eden sanal tablolar gösterilmektedir. Bu tablolar şunları içerir:

* Özgün dizinin satırına karşılık gelen özgün birincil anahtar sütununa geri başvuru (_ID sütunu aracılığıyla)
* Verilerin orijinal dizi içinde konumunun bir göstergesi
* Dizi içindeki her öğe için genişletilmiş veriler

**"ExampleTable_Invoices" tablosu:**

| _kimlik | ExampleTable_Invoices_dim1_idx | invoice_id | Maddesinin | price | İndirim |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0,2 |
| 1111 |1 |124 |oven |1235 |0,2 |
| 2222 |0 |135 |buzdolabı |12543 |0,0 |

**"ExampleTable_Ratings" tablosu:**

| _kimlik | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
