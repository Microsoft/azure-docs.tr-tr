---
title: Verileri içine/Azure Cosmos DB taşıyın
description: Azure Data Factory kullanarak verileri Azure Cosmos DB koleksiyona nasıl taşıyacağınızı öğrenin
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c0ff1b9164f0e631bf148af88bd9efaaaa61f431
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683166"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Cosmos DB veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-azure-documentdb-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de Azure Cosmos DB Bağlayıcısı](../connector-azure-cosmos-db.md)' na bakın.

Bu makalede, verileri Azure Cosmos DB (SQL API) içine/kaynağından taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Desteklenen herhangi bir kaynak veri deposundan verileri, desteklenen herhangi bir havuz veri deposuna Azure Cosmos DB veya Azure Cosmos DB kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın.

> [!IMPORTANT]
> Azure Cosmos DB Bağlayıcısı yalnızca SQL API 'sini destekler.

Verileri, JSON dosyalarını veya başka bir Cosmos DB koleksiyonu olarak kopyalamak için bkz. [JSON belgelerini içeri/dışarı aktarma](#importexport-json-documents).

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak Azure Cosmos DB veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Cosmos DB veri kopyalamak için kullanılan Data Factory varlıkların JSON tanımlarına sahip örnekler için, bu makalenin [JSON örnekleri](#json-examples) bölümüne bakın.

Aşağıdaki bölümler Cosmos DB özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda Azure Cosmos DB bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| type |Type özelliği: **DocumentDb** olarak ayarlanmalıdır |Evet |
| connectionString |Azure Cosmos DB veritabanına bağlanmak için gereken bilgileri belirtin. |Evet |

Örnek:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik & özelliklerinin tam listesi için, lütfen [veri kümesi oluşturma](data-factory-create-datasets.md) makalesine bakın. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Documentdbcollection** türündeki veri kümesinin typeproperties bölümü aşağıdaki özelliklere sahiptir.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| Ma |Cosmos DB belge koleksiyonunun adı. |Evet |

Örnek:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Data Factory şema
Azure Cosmos DB gibi şema içermeyen veri depoları için, Data Factory hizmeti şemayı aşağıdaki yöntemlerle bir arada algılar:

1. Veri kümesi tanımında **Yapı** özelliğini kullanarak verilerin yapısını belirtirseniz, Data Factory hizmeti bu yapıyı şema olarak kabul eder. Bu durumda, bir satır bir sütun için değer içermiyorsa, bunun için bir null değer sağlanacaktır.
2. Veri kümesi tanımında **Yapı** özelliğini kullanarak verilerin yapısını belirtmezseniz, Data Factory hizmeti verilerin ilk satırını kullanarak şemayı algılar. Bu durumda, ilk satır tam şemayı içermiyorsa, kopyalama işleminin sonucunda bazı sütunlar eksik olur.

Bu nedenle, şemaya ücretsiz veri kaynakları için en iyi yöntem, **Yapı** özelliğini kullanarak verilerin yapısını belirtmektir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
& Bölümlerinin tam listesi için, etkinlikleri tanımlamaya yönelik özellikler, lütfen işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği yalnızca bir girdi alır ve yalnızca bir çıktı üretir.

Diğer yandan etkinliğin typeProperties bölümünde bulunan özellikler, her etkinlik türü ve kopyalama etkinliği durumunda, kaynak ve havuz türlerine göre farklılık gösterir.

Kaynak **Documentdbcollectionsource** türünde olduğunda kopyalama etkinliği durumunda şu özellikler **typeproperties** bölümünde mevcuttur:

| **Özellik** | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için sorguyu belirtin. |Sorgu dizesi Azure Cosmos DB tarafından destekleniyor. <br/><br/>Örnek: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Hayır <br/><br/>Belirtilmemişse, çalıştırılan SQL ifadesini: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Belgenin iç içe olduğunu belirten özel karakter |Herhangi bir karakter. <br/><br/>Azure Cosmos DB, iç içe yapılara izin verilen JSON belgeleri için bir NoSQL deposudur. Azure Data Factory, kullanıcının, "." olan nestingSeparator aracılığıyla hiyerarşiyi görüntülemesine olanak sağlar. Yukarıdaki örneklerde. Ayırıcı ile kopyalama etkinliği, tablo tanımındaki "ad. First", "Name. Middle" ve "Name. Last" öğelerine göre Ilk olarak üç alt öğe içeren "ad" nesnesini oluşturur. |Hayır |

**Documentdbcollectionsink** aşağıdaki özellikleri destekler:

| **Özellik** | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| nestingSeparator |İç içe geçmiş belgenin gerekli olduğunu göstermek için kaynak sütun adında özel bir karakter. <br/><br/>Örneğin, yukarıdaki: çıktı tablosundaki `Name.First`, Cosmos DB belgesinde aşağıdaki JSON yapısını üretir:<br/><br/>"Ad": {<br/>    "İlk": "John"<br/>}, |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir.<br/><br/>Varsayılan değer `.` ' dır (nokta). |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir. <br/><br/>Varsayılan değer `.` ' dır (nokta). |
| writeBatchSize |Azure Cosmos DB hizmetine belge oluşturmak için paralel istek sayısı.<br/><br/>Bu özelliği kullanarak Cosmos DB veri kopyalarken performansı ayarlayabilirsiniz. Cosmos DB için daha fazla paralel istek gönderildiği için writeBatchSize ' i artırdığınızda daha iyi bir performans sağlayabilirsiniz. Bununla birlikte, hata iletisini oluşturabilecek azaltmaktan kaçınmanız gerekir: "Istek hızı çok büyük".<br/><br/>Daraltma, belge boyutu, belgelerdeki terim sayısı, hedef koleksiyonun dizin oluşturma ilkesi vb. dahil olmak üzere bir dizi etkene karar vermiştir. Kopyalama işlemleri için, en fazla üretilen iş (2.500 istek birimi/saniye) sağlamak üzere daha iyi bir koleksiyon (ör. S3) kullanabilirsiniz. |Tamsayı |Hayır (varsayılan: 5) |
| writeBatchTimeout |İşlemin zaman aşımına uğramadan önce tamamlanmasını bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |

## <a name="importexport-json-documents"></a>JSON belgelerini içeri/dışarı aktarma
Bu Cosmos DB bağlayıcısını kullanarak kolayca yapabilirsiniz

* Çeşitli kaynaklardan gelen JSON belgelerini Azure blob, Azure Data Lake, şirket içi dosya sistemi veya Azure Data Factory tarafından desteklenen diğer dosya tabanlı depolar dahil Cosmos DB içine aktarın.
* JSON belgelerini Cosmos DB koleksiyonundan çeşitli dosya tabanlı depolara dışarı aktarın.
* İki Cosmos DB koleksiyon arasında veri geçirin.

Bu tür bir şemaya belirsiz kopya elde etmek için
* Kopyalama Sihirbazı 'nı kullanırken, **"olduğu gibi, JSON dosyalarını veya Cosmos DB koleksiyonunu dışarı aktar"** seçeneğini işaretleyin.
* JSON düzenlemesini kullanırken, kopyalama etkinliğinde Cosmos DB kaynak/havuz üzerinde Cosmos DB veri kümesinde ve "nestingSeparator" özelliğindeki "Structure" bölümünü belirtmeyin. JSON dosyalarını içeri/dışarı aktarmak için, dosya deposu veri kümesinde biçim türünü "JsonFormat", config "Filemodel" olarak belirleyin ve REST biçim ayarlarını atlayın ve Ayrıntılar için [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format) bölümüne bakın.

## <a name="json-examples"></a>JSON örnekleri
Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Azure Cosmos DB ve Azure Blob depolama alanına veri kopyalamayı gösterir. Ancak, veriler, Azure Data Factory ' deki kopyalama [etkinliği kullanılarak herhangi](data-factory-data-movement-activities.md#supported-data-stores-and-formats) bir kaynaktan herhangi bir havuza **doğrudan** kopyalanabilir.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Örnek: Azure Cosmos DB verileri Azure Blob 'a kopyalama
Aşağıdaki örnek gösterilmektedir:

1. [DocumentDb](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Documentdbcollection](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Documentdbcollectionsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, Azure Cosmos DB verilerini Azure Blob 'a kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure Cosmos DB bağlı hizmet:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob depolama bağlı hizmeti:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure belge DB giriş veri kümesi:**

Örnek, Azure Cosmos DB veritabanında **kişi** adında bir koleksiyona sahip olduğunuzu varsayar.

"External": "true" olarak ayarlanıyor ve externalData ilke bilgilerini belirtmek, tablonun veri fabrikasında dış olduğu ve veri fabrikasında bir etkinlik tarafından üretilmeyen Azure Data Factory hizmetidir.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure Blob çıktı veri kümesi:**

Veriler, saat ayrıntı düzeyi ile belirli bir tarih ve saati yansıtan blob yolu ile her saat yeni bir bloba kopyalanır.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Cosmos DB veritabanındaki kişi koleksiyonundaki örnek JSON belgesi:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB hiyerarşik JSON belgeleri üzerinde SQL benzeri sözdizimi kullanarak belge sorgulamayı destekler.

Örnek:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Aşağıdaki işlem hattı, verileri Azure Cosmos DB veritabanındaki kişi koleksiyonundan Azure blobuna kopyalar. Kopyalama etkinliğinin bir parçası olarak giriş ve çıkış veri kümeleri belirtilmiştir.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Örnek: Azure Blobundan Azure Cosmos DB verileri kopyalama
Aşağıdaki örnek gösterilmektedir:

1. DocumentDb türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. DocumentDbCollection türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) ve DocumentDbCollectionSink kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri Azure blobundan Azure Cosmos DB olarak kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure Blob depolama bağlı hizmeti:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Cosmos DB bağlı hizmet:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob giriş veri kümesi:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB çıktı veri kümesi:**

Örnek, verileri "Person" adlı bir koleksiyona kopyalar.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Aşağıdaki işlem hattı, verileri Azure Blobundan Cosmos DB kişi koleksiyonuna kopyalar. Kopyalama etkinliğinin bir parçası olarak giriş ve çıkış veri kümeleri belirtilmiştir.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Örnek blob girişi ise

```
1,John,,Doe
```
Daha sonra, Cosmos DB JSON çıktısı şöyle olacaktır:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB, iç içe yapılara izin verilen JSON belgeleri için bir NoSQL deposudur. Azure Data Factory, kullanıcının, "." olan **Nestingseparator**aracılığıyla hiyerarşiyi görüntülemesine olanak sağlar. Bu örnekte. Ayırıcı ile kopyalama etkinliği, tablo tanımındaki "ad. First", "Name. Middle" ve "Name. Last" öğelerine göre Ilk olarak üç alt öğe içeren "ad" nesnesini oluşturur.

## <a name="appendix"></a>Ek
1. **Soru:** Kopyalama etkinliği mevcut kayıtların güncelleştirilmesini destekliyor mu?

    **Cevap:** Eşleşen.
2. **Soru:** Azure Cosmos DB kopyası, zaten kopyalanmış kayıtlarla nasıl yeniden denenirken?

    **Cevap:** Kayıtlarda bir "ID" alanı varsa ve kopyalama işlemi aynı KIMLIĞE sahip bir kayıt eklemeye çalışırsa, kopyalama işlemi bir hata oluşturur.
3. **Soru:** [Aralık veya karma tabanlı veri bölümlendirme](../../cosmos-db/sql-api-partition-data.md)Data Factory destekler mi?

    **Cevap:** Eşleşen.
4. **Soru:** Tablo için birden fazla Azure Cosmos DB koleksiyonu belirtebilir miyim?

    **Cevap:** Eşleşen. Şu anda yalnızca bir koleksiyon belirtilebilir.

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
