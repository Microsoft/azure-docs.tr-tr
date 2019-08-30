---
title: / Azure Cosmos DB'den verileri taşıma | Microsoft Docs
description: Bilgi nasıl hareket veri gönderip buralardan Azure Data Factory kullanarak Azure Cosmos DB koleksiyonu
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
ms.openlocfilehash: 1016a7800ddcfd4066ec3f6d6dce00d01ad83471
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839498"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Cosmos DB gelen ve giden veri taşıma
> [!div class="op_single_selector" title1="Data Factory hizmetinin kullandığınız sürümü seçin:"]
> * [Sürüm 1](data-factory-azure-documentdb-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz [V2'de Azure Cosmos DB Bağlayıcısı](../connector-azure-cosmos-db.md).

Bu makalede, kopyalama etkinliği Azure Data Factory'de veri gönderip buralardan veri Azure Cosmos DB (SQL API) taşımak için nasıl kullanılacağı açıklanmaktadır. Yapılar [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi, kopyalama etkinliği ile verileri taşıma genel bir bakış sunar.

Tüm Azure Cosmos DB için desteklenen kaynak veri deposundan veya herhangi bir desteklenen havuz veri deposu için Azure Cosmos DB'den verileri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak ve havuz desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablo.

> [!IMPORTANT]
> Azure Cosmos DB Bağlayıcısı, yalnızca SQL API'sini destekler.

Olarak veri kopyalama-olduğu için/JSON dosyaları veya başka bir Cosmos DB koleksiyonu bkz [içeri/dışarı aktarma JSON belgelerini](#importexport-json-documents).

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API'lerini kullanarak Azure Cosmos DB/deposundan veri taşıyan kopyalama etkinliği ile işlem hattı oluşturabilirsiniz.

Bir işlem hattı oluşturmanın en kolay yolu kullanmaktır **Kopyalama Sihirbazı'nı**. Bkz: [Öğreticisi: Kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) veri kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma hızlı bir kılavuz.

Ayrıca, bir işlem hattı oluşturmak için aşağıdaki araçları kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**, ve **REST API**. Bkz: [kopyalama etkinliği Öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kopyalama etkinliği ile işlem hattı oluşturmak adım adım yönergeler için.

API'ler ve Araçlar kullanmanıza bakılmaksızın, bir havuz veri deposu için bir kaynak veri deposundan veri taşıyan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirin:

1. Oluşturma **bağlı hizmetler** girdi ve çıktı verilerini bağlamak için veri fabrikanıza depolar.
2. Oluşturma **veri kümeleri** kopyalama işleminin girdi ve çıktı verilerini göstermek için.
3. Oluşturma bir **işlem hattı** bir veri kümesini girdi ve çıktı olarak bir veri kümesini alan kopyalama etkinliği ile.

Sihirbazı'nı kullandığınızda, bu Data Factory varlıklarını (bağlı hizmetler, veri kümeleri ve işlem hattı) için JSON tanımları sizin için otomatik olarak oluşturulur. Araçlar/API'leri (dışında .NET API'si) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlayın. / Cosmos DB'den verileri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları ile örnekleri için bkz [JSON örnekler](#json-examples) bu makalenin.

Aşağıdaki bölümler, Data Factory varlıklarını belirli Cosmos DB'ye tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri
Aşağıdaki tabloda, Azure Cosmos DB bağlı hizmeti için özel JSON öğeleri için bir açıklama sağlar.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| type |Type özelliği ayarlanmalıdır: **DocumentDb** |Evet |
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
Bölümleri ve veri kümeleri tanımlamak için mevcut özelliklerin tam listesi için lütfen başvurmak [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bölümler bir veri kümesi JSON İlkesi yapısı ve kullanılabilirlik gibi tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablo, vs.) için benzer.

TypeProperties bölümünün her tür veri kümesi için farklıdır ve verilerin veri deposundaki konumu hakkında bilgi sağlar. TypeProperties bölüm türü için veri kümesi **DocumentDbCollection** aşağıdaki özelliklere sahiptir.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| collectionName |Cosmos DB belge koleksiyonu adı. |Evet |

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
### <a name="schema-by-data-factory"></a>Veri fabrikası tarafından şeması
Azure Cosmos DB gibi şemasız veri depoları için Data Factory hizmetinin şemayı aşağıdaki yollardan biriyle algılar:

1. Verilerin yapısını kullanarak belirtirseniz **yapısı** veri kümesi tanımında, Data Factory hizmetinin özelliği, şema olarak bu yapı geliştirir. Bir satır bir sütun için bir değer içermiyorsa, bu durumda, bir null değer için sağlanır.
2. Veri yapısını kullanarak belirtmezseniz **yapısı** özelliği veri kümesi tanımında, Data Factory hizmetinin çıkarsar şema verilerin ilk satırı kullanarak. Bu durumda, ilk satır, tam şema içermiyorsa, bazı sütunları kopyalama işleminin sonucunda eksik olacaktır.

Bu nedenle, şemasız veri kaynakları için veri kullanımının yapısı belirtmek için en iyi yöntem olacaktır **yapısı** özelliği.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Bölümleri & etkinlikleri tanımlamak için mevcut özelliklerin tam listesi için lütfen başvurmak [işlem hatları oluşturma](data-factory-create-pipelines.md) makalesi. İlke adı ve açıklaması, girdi ve çıktı tabloları gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği, tek bir girdi alır ve tek bir çıktı üretir.

Etkinliğin typeProperties bölümündeki özellikler her etkinlik türü ile diğer yandan değişir ve kopyalama etkinliği olması durumunda, kaynaklar ve havuzlar türlerine bağlı olarak farklılık gösterir.

Kopyalama etkinliği kaynak türü olduğunda durumunda **DocumentDbCollectionSource** aşağıdaki özellikler kullanılabilir **typeProperties** bölümü:

| **Özellik** | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| query |Verileri okumak için bir sorgu belirtin. |Azure Cosmos DB tarafından desteklenen dize sorgulayın. <br/><br/>Örnek: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Hayır <br/><br/>Belirtilmezse, yürütülen SQL deyimi: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Belge iç içe geçmiş belirtmek için özel karakter |Herhangi bir karakter. <br/><br/>Azure Cosmos DB iç içe geçmiş yapılar burada izin verilen bir NoSQL JSON belgeleri için deposudur. Azure Data Factory sağlayan kullanıcı hiyerarşisi olan nestingSeparator aracılığıyla belirtmek "." Yukarıdaki örneklerde. Ayırıcı ile kopyalama etkinliği ile üç alt öğeleri "Name" nesne ilk olarak, oluşturacak Orta ve son olarak, "Name.First", "Name.Middle" ve "Name.Last" Tablo tanımındaki göre. |Hayır |

**DocumentDbCollectionSink** aşağıdaki özellikleri destekler:

| **Özellik** | **Açıklama** | **İzin verilen değerler** | **Gerekli** |
| --- | --- | --- | --- |
| nestingSeparator |Bir iç içe geçmiş belge belirtmek için kaynak sütun adı özel karakterler gereklidir. <br/><br/>Örneğin yukarıdaki: `Name.First` Cosmos DB belgesini aşağıdaki JSON yapısında tablo çıktısında oluşturur:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir.<br/><br/>Varsayılan değer `.` (nokta). |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir. <br/><br/>Varsayılan değer `.` (nokta). |
| writeBatchSize |Belgeleri oluşturmak için Azure Cosmos DB hizmetine paralel isteklerinin sayısı.<br/><br/>Bu özelliği kullanarak veri gönderip buralardan veri Cosmos DB kopyalarken performans hassas ayarlamalar yapabilirsiniz. Cosmos DB için daha fazla paralel istekler gönderildiği writeBatchSize artırdığınızda daha iyi bir performans bekleyebilirsiniz. Ancak, azaltmayı önlemek gerekir, hata iletisi oluşturabilecek: "İstek oranı büyük".<br/><br/>Azaltma, belgeler, belgeleri koşullarını sayısı boyutu da dahil olmak üzere, dizin oluşturma ilkesi hedef koleksiyon, vb. faktörleri sayısına göre belirlenir. Kopyalama işlemleri için en iyi kullanılabilir işleme sağlamak için daha iyi bir koleksiyon (örn. S3) kullanabilirsiniz (2.500 istek birimi/saniye). |Tamsayı |Hayır (varsayılan: 5) |
| writeBatchTimeout |İşlem zaman aşımına uğramadan önce tamamlanması için bir süre bekleyin. |TimeSpan<br/><br/> Örnek: "00: 30:00" (30 dakika). |Hayır |

## <a name="importexport-json-documents"></a>İçeri/dışarı aktarma JSON belgeleri
Bu Cosmos DB Bağlayıcısı'nı kullanarak, kolayca yapabilecekleriniz

* JSON belgelerini Cosmos DB, Azure Blob, Azure Data Lake, şirket içi dosya sistemine veya Azure Data Factory tarafından desteklenen diğer dosya tabanlı depoları gibi çeşitli kaynaklardan veri aktarın.
* JSON belgelerini çeşitli dosya tabanlı depoları Cosmos DB koleksiyonu dışarı.
* İki Cosmos DB koleksiyonları arasında veri geçirme-olduğu.

Bu tür şemadan kopyalama elde etmek için
* Kopyalama Sihirbazı'nı kullanırken, kontrol **"Dışarı Aktar-JSON dosyaları veya Cosmos DB koleksiyonu"** seçeneği.
* Ne zaman JSON düzenleme, kullanarak belirtme "yapı" bölümü Cosmos DB veri kümelerindeki ya da "nestingSeparator" özelliği Cosmos DB üzerinde kaynak/havuz kopyalama etkinliği. JSON dosyalarına dışarı aktarmanızı / almak için dosya depolama kümesinde "JsonFormat", "filePattern" yapılandırma biçim türü belirtin ve rest biçimi ayarları atlamak için bkz [JSON biçimine](data-factory-supported-file-and-compression-formats.md#json-format) ayrıntıları bölümü.

## <a name="json-examples"></a>JSON örnekleri
Aşağıdaki örnekler kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımları sağlamak [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Bunlar, gelen Azure Cosmos DB ile Azure Blob Depolama ve veri kopyalama işlemini göstermektedir. Ancak, veriler kopyalanabilir **doğrudan** herhangi birinden herhangi birine belirtilen havuzlarını kaynakları [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopyalama etkinliğini kullanarak Azure Data Factory'de.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Örnek: Azure Blob için Azure Cosmos DB'den verileri kopyalama
Aşağıdaki örnekte gösterilmektedir:

1. Bağlı hizmet türü [DocumentDb](#linked-service-properties).
2. Bağlı hizmet türü [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [DocumentDbCollection](#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) türü [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [işlem hattı](data-factory-create-pipelines.md) kullanan bir kopyalama etkinliği ile [DocumentDbCollectionSource](#copy-activity-properties) ve [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Örnek verileri Azure Blob için Azure Cosmos DB'de kopyalar. Bu örneklerde kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

**Azure Cosmos DB bağlı hizmeti:**

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
**Azure Blob Depolama bağlı hizmeti:**

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
**Azure Document DB girdi veri kümesi:**

Örnek adlı bir koleksiyon olduğunu varsayar **kişi** bir Azure Cosmos DB veritabanı.

"Dış" ayarını: "true" ve tablo harici veri fabrikasına ve veri fabrikasında bir etkinliği tarafından üretilen değil externalData ilke bilgilerini Azure Data Factory hizmetinin belirtme.

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

Veriler belirli tarih ve saat ile saat ayrıntı düzeyi yansıtan blob yolu ile her saat yeni bir bloba kopyalanır.

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
Bir Cosmos DB veritabanı'nda kişinin koleksiyondaki örnek JSON belgesi:

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
Cosmos DB hiyerarşik JSON belgelerini SQL gibi bir söz dizimi kullanarak belgelerin sorgulanmasını destekler.

Örnek:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Aşağıdaki işlem hattı verileri Azure blobuna Azure Cosmos DB veritabanı'nda kişinin koleksiyondan kopyalar. Veri kümeleri bölümü kopyalama etkinliğinin giriş ve çıkış belirtildi.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Örnek: Verileri Azure Blobundan Azure Cosmos DB'ye kopyalayın.
Aşağıdaki örnekte gösterilmektedir:

1. Bağlı hizmet türü DocumentDb.
2. Bağlı hizmet türü [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) DocumentDbCollection türü.
5. A [işlem hattı](data-factory-create-pipelines.md) kullanan bir kopyalama etkinliği ile [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) ve DocumentDbCollectionSink.

Örnek verileri Azure'dan kopyalar. Azure Cosmos DB için blob. Bu örneklerde kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

**Azure Blob Depolama bağlı hizmeti:**

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
**Azure Cosmos DB bağlı hizmeti:**

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
**Azure Blob girdi veri kümesi:**

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

Örnek verileri "Kişi" adlı bir koleksiyona kopyalar.

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
Aşağıdaki işlem hattı Azure Blobundan verileri Cosmos DB kişi koleksiyonda kopyalar. Veri kümeleri bölümü kopyalama etkinliğinin giriş ve çıkış belirtildi.

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
Örnek blob girdi olarak ise

```
1,John,,Doe
```
Ardından çıktı Cosmos DB'de JSON olarak olacaktır:

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
Azure Cosmos DB iç içe geçmiş yapılar burada izin verilen bir NoSQL JSON belgeleri için deposudur. Azure Data Factory aracılığıyla hiyerarşisini göstermek kullanıcı sağlar **nestingSeparator**, olan "." Bu örnekte. Ayırıcı ile kopyalama etkinliği ile üç alt öğeleri "Name" nesne ilk olarak, oluşturacak Orta ve son olarak, "Name.First", "Name.Middle" ve "Name.Last" Tablo tanımındaki göre.

## <a name="appendix"></a>Ek
1. **Soru:** Kopyalama etkinliği destek var olan kayıtların güncelleştirmesi mu?

    **Yanıt:** Hayır.
2. **Soru:** Azure Cosmos DB'ye bir kopyasını yeniden denemek, zaten kopyalanan kayıtlarla nasıl dağıtılsın mı?

    **Yanıt:** Kopyalama işlemi, kayıtları bir "ID" alanına sahip ve aynı Kimliğe sahip bir kayıt eklemek kopyalama işlemi çalışırsa bir hata oluşturur.
3. **Soru:** Data Factory desteklemiyor [aralığı veya karma tabanlı veri bölümleme](../../cosmos-db/sql-api-partition-data.md)?

    **Yanıt:** Hayır.
4. **Soru:** Ben bir tablo için birden fazla Azure Cosmos DB koleksiyonu belirtebilir miyim?

    **Yanıt:** Hayır. Şu anda yalnızca bir koleksiyon belirtilebilir.

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Bkz: [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md) veri taşıma (kopyalama etkinliği) Azure Data Factory ve bunu en iyi duruma getirmek için çeşitli yollar, performansı etkileyebilir anahtar Etkenler hakkında bilgi edinmek için.
