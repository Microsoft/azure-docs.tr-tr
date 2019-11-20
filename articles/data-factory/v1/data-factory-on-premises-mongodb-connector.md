---
title: Data Factory kullanarak MongoDB 'den veri taşıma
description: Azure Data Factory kullanarak MongoDB veritabanından veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6f982928e706b442229cc249c17c3f7aabe1f60a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666649"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory kullanarak MongoDB 'Den veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-on-premises-mongodb-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-mongodb.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de MongoDB Bağlayıcısı](../connector-mongodb.md).


Bu makalede, verileri şirket içi MongoDB veritabanından taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Şirket içi bir MongoDB veri deposundaki verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca bir MongoDB veri deposundaki verileri diğer veri depolarına taşımayı destekler, ancak verileri diğer veri depolarından MongoDB veri deposuna taşımak için kullanmaz.

## <a name="prerequisites"></a>Ön koşullar
Azure Data Factory hizmetinin şirket içi MongoDB veritabanınıza bağlanabilmesi için aşağıdaki bileşenleri yüklemelisiniz:

- Desteklenen MongoDB sürümleri şunlardır: 2,4, 2,6, 3,0, 3,2, 3,4 ve 3,6.
- Veritabanını barındıran aynı makinede veya veritabanına sahip kaynakların rekabeti önlemek için ayrı bir makinede Veri Yönetimi ağ geçidi. Veri Yönetimi ağ geçidi, şirket içi veri kaynaklarını güvenli ve yönetilen bir şekilde bulut hizmetlerine bağlayan bir yazılımdır. Veri Yönetimi ağ geçidi hakkındaki ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) makalesi. Verileri taşımak için bir veri işlem hattı ayarlama hakkında adım adım yönergeler için bkz. [Şirket içinden buluta veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi.

    Ağ geçidini yüklediğinizde, MongoDB 'ye bağlanmak için kullanılan bir Microsoft MongoDB ODBC sürücüsünü otomatik olarak yükler.

    > [!NOTE]
    > Azure IaaS VM 'lerinde barındırıldığından bile MongoDB 'ye bağlanmak için ağ geçidini kullanmanız gerekir. Bulutta barındırılan bir MongoDB örneğine bağlanmaya çalışıyorsanız, ağ geçidi örneğini IaaS VM 'sine de yükleyebilirsiniz.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak şirket içi MongoDB veri deposundaki verileri taşıyan kopyalama etkinliğiyle bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Şirket içi MongoDB veri deposundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarına sahip bir örnek için, bu makalenin [JSON örnek: verileri MongoDB 'Den Azure Blob 'A kopyalama](#json-example-copy-data-from-mongodb-to-azure-blob) bölümüne bakın.

Aşağıdaki bölümler, MongoDB kaynağına özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, **OnPremisesMongoDB** bağlı HIZMETINE özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **OnPremisesMongoDb** olarak ayarlanmalıdır |Evet |
| sunucu |MongoDB sunucusunun IP adresi veya ana bilgisayar adı. |Evet |
| port |MongoDB sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. |İsteğe bağlı, varsayılan değer: 27017 |
| authenticationType |Temel veya anonim. |Evet |
| kullanıcı adı |MongoDB 'ye erişmek için Kullanıcı hesabı. |Evet (temel kimlik doğrulaması kullanılıyorsa). |
| password |Kullanıcının parolası. |Evet (temel kimlik doğrulaması kullanılıyorsa). |
| authSource |Kimlik doğrulaması için kimlik bilgilerinizi denetlemek üzere kullanmak istediğiniz MongoDB veritabanının adı. |İsteğe bağlı (temel kimlik doğrulaması kullanılıyorsa). Varsayılan: yönetici hesabını ve databaseName özelliği kullanılarak belirtilen veritabanını kullanır. |
| Dosyasında |Erişmek istediğiniz MongoDB veritabanının adı. |Evet |
| gatewayName |Veri deposuna erişen ağ geçidinin adı. |Evet |
| encryptedCredential |Ağ Geçidi tarafından şifrelenen kimlik bilgileri. |İsteğe bağlı |

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Mongodbcollection** türündeki veri kümesinin typeproperties bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Ma |MongoDB veritabanındaki koleksiyonun adı. |Evet |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Diğer yandan etkinliğin **Typeproperties** bölümünde bulunan özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Kaynak **Mongodbsource** türünde olduğunda, typeproperties bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL-92 sorgu dizesi. Örneğin: select * from MyTable. |Hayır ( **veri kümesi** **KoleksiyonAdı** belirtilmişse) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON örneği: MongoDB 'den Azure Blob 'a veri kopyalama
Bu örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Şirket içi MongoDB 'den bir Azure Blob depolama alanına nasıl veri kopyalanacağını gösterir. Ancak, veriler burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza kopyalanabilir.

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

1. [OnPremisesMongoDb](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Mongodbcollection](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Mongodbsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, bir sorgudaki verileri MongoDB veritabanında her saat bir bloba bir blob 'a kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) makalesindeki yönergelere göre ayarlayın.

**MongoDB bağlı hizmeti:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Azure depolama bağlı hizmeti:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**MongoDB giriş veri kümesi:** "External": "true" ayarı, Data Factory hizmetine tablonun veri fabrikasında dış olduğunu bildirir ve veri fabrikasında bir etkinlik tarafından üretilmez.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**MongoDB kaynağına ve BLOB havuzuna sahip bir işlem hattındaki etkinliği kopyalama:**

İşlem hattı, yukarıdaki giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında, **kaynak** türü **Mongodbsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Data Factory şema
Azure Data Factory hizmet, koleksiyondaki en son 100 belgeyi kullanarak bir MongoDB koleksiyonundan şemayı hallederler. Bu 100 belgeler tam şema içermiyorsa, kopyalama işlemi sırasında bazı sütunlar yoksayılabilir.

## <a name="type-mapping-for-mongodb"></a>MongoDB için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi kopyalama etkinliği, aşağıdaki 2 adımlı yaklaşımla kaynak türlerindeki otomatik tür dönüştürmeleri, havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Verileri MongoDB 'ye taşırken, MongoDB türlerinden .NET türlerine aşağıdaki eşlemeler kullanılır.

| MongoDB türü | .NET Framework türü |
| --- | --- |
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
> Sanal tabloları kullanarak diziler için destek hakkında bilgi edinmek için aşağıdaki [sanal tabloları kullanarak karmaşık türler Için destek](#support-for-complex-types-using-virtual-tables) bölümüne bakın.

Şu anda şu MongoDB veri türleri desteklenmez: DBPointer, JavaScript, Max/Min anahtarı, normal Ifade, simge, zaman damgası, tanımsız

## <a name="support-for-complex-types-using-virtual-tables"></a>Sanal tabloları kullanarak karmaşık türler için destek
Azure Data Factory, MongoDB veritabanınıza bağlanmak ve veri kopyalamak için yerleşik bir ODBC sürücüsü kullanır. Belge genelinde farklı türlere sahip diziler veya nesneler gibi karmaşık türler için, sürücü verileri ilgili sanal tablolara yeniden normalleştirir. Özellikle, bir tablo bu tür sütunlar içeriyorsa, sürücü aşağıdaki sanal tabloları oluşturur:

* Karmaşık tür sütunları hariç gerçek tabloyla aynı verileri içeren bir **temel tablo**. Temel tablo, temsil ettiği gerçek tabloyla aynı adı kullanır.
* İç içe geçmiş verileri genişleten her karmaşık tür sütunu için bir **sanal tablo** . Sanal tablolar, gerçek tablonun adı, bir ayırıcı "_" ve dizi ya da nesnenin adı kullanılarak adlandırılır.

Sanal tablolar, gerçek tablodaki verilere başvurur ve bu da sürücünün, verilerin yoğun verilere erişmesine olanak tanır. Ayrıntılar bölümündeki örnek bölümüne bakın. Sanal tabloları sorgulama ve birleştirme yoluyla MongoDB dizilerinin içeriğine erişebilirsiniz.

[Kopyalama Sihirbazı](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) 'Nı kullanarak MongoDB veritabanında sanal tablolar dahil olmak üzere Tablo listesini görüntüleyebilir ve içindeki verilerin önizlemesini görebilirsiniz. Ayrıca kopyalama sihirbazında bir sorgu oluşturabilir ve sonucu görmek için doğrulayabilirsiniz.

### <a name="example"></a>Örnek
Örneğin, aşağıdaki "ExampleTable" tablosu, her hücrede bir nesne dizisi olan tek bir sütun içeren bir MongoDB tablosu ve bir sütun skaler türlerden oluşan bir dizi içeren bir sütun içerir.

| _kimlik | Müşteri adı | Faturalar | Hizmet Düzeyi | Lendir |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", öğe: "Toaster", Price: "456", indirim: "0.2"}, {invoice_id: "124", öğe: "oven", Fiyat: "1235", indirim: "0,2"}] |Silver |[5, 6] |
| 2222 |V |[{invoice_id: "135", öğe: "Fridge", Fiyat: "12543", indirim: "0,0"}] |Gold |[1, 2] |

Bu tek tabloyu temsil eden sürücü birden çok sanal tablo oluşturur. İlk sanal tablo, aşağıda gösterildiği gibi "ExampleTable" adlı temel tablodur. Temel tablo özgün tablonun tüm verilerini içerir, ancak dizilerdeki veriler atlanmıştır ve sanal tablolarda genişletilir.

| _kimlik | Müşteri adı | Hizmet Düzeyi |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |V |Gold |

Aşağıdaki tablolarda, örnekteki özgün dizileri temsil eden sanal tablolar gösterilmektedir. Bu tablolar şunları içerir:

* Özgün dizinin satırına karşılık gelen özgün birincil anahtar sütununa geri başvuru (_ID sütunu aracılığıyla)
* Verilerin orijinal dizi içinde konumunun bir göstergesi
* Dizi içindeki her öğe için genişletilmiş veriler

"ExampleTable_Invoices" tablosu:

| _kimlik | ExampleTable_Invoices_dim1_idx | invoice_id | Maddesinin | price | İndirim |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0,2 |
| 1111 |1 |124 |oven |1235 |0,2 |
| 2222 |0 |135 |buzdolabı |12543 |0,0 |

"ExampleTable_Ratings" tablosu:

| _kimlik | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .

## <a name="next-steps"></a>Sonraki Adımlar
Şirket içi bir veri deposundan Azure veri deposuna veri taşınan bir veri işlem hattı oluşturmaya yönelik adım adım yönergeler için bkz. Şirket [içi ve bulut arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi.
