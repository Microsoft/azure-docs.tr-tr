---
title: Data Factory kullanarak verileri Search dizinine gönderme | Microsoft Docs
description: Azure Data Factory kullanarak Azure Search dizinine veri gönderme hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 30a5bc9c5f0b7d1443e7ca2a16d9f0e0d1120dd8
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836638"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Veri göndermek için Azure Data Factory kullanarak Azure Search dizini
> [!div class="op_single_selector" title1="Data Factory hizmetinin kullandığınız sürümü seçin:"]
> * [Sürüm 1](data-factory-azure-search-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-search.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz [V2'de Azure Search bağlayıcı](../connector-azure-search.md).

Bu makalede, Azure Search dizini için desteklenen kaynak veri deposundan veri göndermek için kopyalama etkinliği kullanmayı açıklar. Desteklenen kaynak veri depolarını Kaynak sütununda listelenen [desteklenen kaynaklar ve havuzlar](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablo. Bu makalede yapılar [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi, kopyalama etkinliği ve desteklenen veri deposu bileşimleri ile veri taşıma genel bir bakış sunar.

## <a name="enabling-connectivity"></a>Bağlantıyı etkinleştirme
Data Factory hizmetine bağlanmak için bir şirket içi veri deposuna izin vermek için veri yönetimi ağ geçidi şirket içi ortamınıza yükleyin. Ağ geçidi ana kaynak verileri depolayan aynı makinede veya veri deposu kaynak için rekabet önlemek için ayrı bir makineye yükleyebilirsiniz.

Veri Yönetimi ağ geçidi, bulut hizmetlerine güvenli ve yönetilen bir şekilde şirket içi veri kaynaklarına bağlanır. Bkz: [şirket içi ile bulut arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) veri yönetimi ağ geçidi hakkında bilgi için makalenin.

## <a name="getting-started"></a>Başlarken
Azure Search dizini için bir kaynak veri deposundan farklı araçları/API'lerini kullanarak veri gönderen bir kopyalama etkinlikli bir işlem hattı oluşturabilirsiniz.

Bir işlem hattı oluşturmanın en kolay yolu kullanmaktır **Kopyalama Sihirbazı'nı**. Bkz: [Öğreticisi: Kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) veri kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma hızlı bir kılavuz.

Ayrıca, bir işlem hattı oluşturmak için aşağıdaki araçları kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**, ve **REST API**. Bkz: [kopyalama etkinliği Öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kopyalama etkinliği ile işlem hattı oluşturmak adım adım yönergeler için.

API'ler ve Araçlar kullanmanıza bakılmaksızın, bir havuz veri deposu için bir kaynak veri deposundan veri taşıyan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirin:

1. Oluşturma **bağlı hizmetler** girdi ve çıktı verilerini bağlamak için veri fabrikanıza depolar.
2. Oluşturma **veri kümeleri** kopyalama işleminin girdi ve çıktı verilerini göstermek için.
3. Oluşturma bir **işlem hattı** bir veri kümesini girdi ve çıktı olarak bir veri kümesini alan kopyalama etkinliği ile.

Sihirbazı'nı kullandığınızda, bu Data Factory varlıklarını (bağlı hizmetler, veri kümeleri ve işlem hattı) için JSON tanımları sizin için otomatik olarak oluşturulur. Araçlar/API'leri (dışında .NET API'si) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlayın.  Azure Search dizinine veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları ile bir örnek için bkz. [JSON örneği: Verileri şirket içi SQL Server'dan Azure Search dizinine kopyalamak](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) bu makalenin.

Aşağıdaki bölümler, Data Factory varlıklarını belirli Azure Search dizinine tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri

Aşağıdaki tabloda, Azure Search bağlantılı hizmete özgü JSON öğelerinin açıklamaları verilmiştir.

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| type | Type özelliği ayarlanmalıdır: **AzureSearch**. | Evet |
| url | Azure Search hizmeti için URL. | Evet |
| key | Azure Search hizmeti için yönetici anahtarı. | Evet |

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Bölümleri ve veri kümeleri tanımlamak için kullanılabilir olan özellikleri tam listesi için bkz: [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bölümler bir veri kümesi JSON İlkesi yapısı ve kullanılabilirlik gibi tüm veri kümesi türleri için benzerdir. **TypeProperties** bölümünde her veri kümesi türü için farklıdır. TypeProperties bölüm türü için bir veri kümesi **AzureSearchIndex** aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| -------- | ----------- | -------- |
| type | Type özelliği ayarlanmalıdır **AzureSearchIndex**.| Evet |
| indexName | Azure Search dizininin adı. Veri fabrikası, Dizin oluşturulmaz. Azure Search'te dizin varolmalıdır. | Evet |


## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Bölümleri ve etkinlikleri tanımlamak için kullanılabilir olan özellikleri tam listesi için bkz: [komut zincirleri oluşturma](data-factory-create-pipelines.md) makalesi. Ad, açıklama, giriş ve çıkış tabloları ve çeşitli ilkeleri gibi özellikler, tüm etkinlik türleri için kullanılabilir. Oysa typeProperties bölümündeki özellikler her etkinlik türü ile farklılık gösterir. Kopyalama etkinliği için kaynaklar ve havuzlar türlerine bağlı olarak farklılık gösterir.

Kopyalama etkinliği Havuz türü olduğunda, **AzureSearchIndexSink**, typeProperties bölümünde aşağıdaki özellikler kullanılabilir:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Bir belge dizinde zaten mevcut olduğunda değiştirin ya da birleştirme belirtir. Bkz: [WriteBehavior özelliği](#writebehavior-property).| (Varsayılan) birleştirme<br/>Karşıya Yükle| Hayır |
| WriteBatchSize | Arabellek boyutu writeBatchSize ulaştığında, verileri Azure Search dizinine yükler. Bkz: [WriteBatchSize özelliği](#writebatchsize-property) Ayrıntılar için. | 1 ila 1.000. Varsayılan değer 1000'dir. | Hayır |

### <a name="writebehavior-property"></a>WriteBehavior özelliği
Veri yazarken AzureSearchSink upsert eder. Diğer bir deyişle, Azure Search dizin belge anahtarı zaten varsa belge yazarken, Azure Search çakışma özel durum yerine var olan bir belgeyi güncelleştirir.

AzureSearchSink (Azure Search SDK'sı kullanılarak) aşağıdaki iki upsert davranışları sağlar:

- **Birleştirme**: var olan bir yeni belge içindeki tüm sütunları birleştirin. Yeni belge null değere sahip sütunları için var olan bir değeri korunur.
- **Karşıya yükleme**: Var olan bir yeni belge değiştirir. Yeni belge içinde belirtilmeyen sütunlar için değer olup olmadığını null olmayan bir değer var olan bir belgeyi veya null olarak ayarlanır.

Varsayılan davranış **birleştirme**.

### <a name="writebatchsize-property"></a>WriteBatchSize özelliği
Azure arama hizmeti, toplu iş olarak belge yazma destekler. Bir toplu iş 1 ila 1.000 eylemleri içerebilir. Karşıya yükleme/birleştirme işlemi gerçekleştirmek için bir belge bir eylem gerçekleştirir.

### <a name="data-type-support"></a>Veri türü desteği
Aşağıdaki tabloda, bir Azure Search veri türü veya desteklenip desteklenmediğini belirtir.

| Azure Search veri türü | Azure Search'ü havuz desteklenen |
| ---------------------- | ------------------------------ |
| Dize | E |
| Int32 | E |
| Int64 | E |
| Double | E |
| Boolean | E |
| DataTimeOffset | E |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON örneği: Verileri şirket içi SQL Server'dan Azure Search dizinine kopyalayın.

Aşağıdaki örnek, gösterir:

1. Bağlı hizmet türü [Azure Search](#linked-service-properties).
2. Bağlı hizmet türü [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) türü [AzureSearchIndex](#dataset-properties).
4. A [işlem hattı](data-factory-create-pipelines.md) kullanan bir kopyalama etkinlikli [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) ve [AzureSearchIndexSink](#copy-activity-properties).

Örnek zaman serisi verilerinin bir şirket içi SQL Server veritabanındaki verileri Azure Search dizini için saatlik kopyalar. Bu örnekte kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

İlk adım, veri yönetimi ağ geçidi, şirket içi makinenizde ayarlayın. Yönergeleri bulunan [Bulut ve şirket içi konumlar arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi.

**Azure arama bağlı hizmeti:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server bağlı hizmeti**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server girdi veri kümesi**

Örnek, "MyTable" SQL Server'da bir tablo oluşturdunuz ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiği varsayılır. Tek bir veri kümesi kullanarak aynı veritabanı içinde birden çok tablolar üzerinden sorgulama yapabilirsiniz, ancak tek bir tabloya veri kümesinin tableName typeProperty için kullanılmalıdır.

"Dış" ayarını: "true" bildirir Data Factory hizmetinin veri kümesi dış veri fabrikasına ve veri fabrikasında bir etkinliği tarafından üretilen değil.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Search'ü çıktı veri kümesi:**

Örnek adlı bir Azure Search dizinine verileri kopyalayan **ürünleri**. Veri fabrikası, Dizin oluşturulmaz. Örnek test etmek için bu ada sahip bir dizin oluşturun. İle aynı sayıda sütun giriş veri kümesi gibi Azure Search dizini oluşturma. Yeni girişler saatte Azure Search dizinine eklenir.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**SQL kaynak ve havuz Azure Search dizini ile bir işlem hattındaki kopyalama etkinliği:**

İşlem hattının giriş ve çıkış veri kümelerini kullanmak için yapılandırıldığı ve saatte bir çalışacak şekilde zamanlanmış bir kopyalama etkinliği içeriyor. JSON tanımı, işlem hattındaki **kaynak** türü ayarlandığında **SqlSource** ve **havuz** türü ayarlandığında **AzureSearchIndexSink**. SQL sorgusu için belirtilen **SqlReaderQuery** özelliği veri kopyalamak için son bir saat içinde seçer.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Azure Search'e, bir bulut veri deposundan veri kopyalıyorsanız `executionLocation` özelliği gereklidir. Aşağıdaki JSON kod parçacığı kopyalama etkinliği altında gereken değişiklik gösteren `typeProperties` örnek olarak. Denetleme [bulut veri depoları arasında veri kopyalama](data-factory-data-movement-activities.md#global) bölümünde desteklenen değerler ve daha fazla ayrıntı için.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Bir bulut kaynağından kopyalayın
Azure Search'e, bir bulut veri deposundan veri kopyalıyorsanız `executionLocation` özelliği gereklidir. Aşağıdaki JSON kod parçacığı kopyalama etkinliği altında gereken değişiklik gösteren `typeProperties` örnek olarak. Denetleme [bulut veri depoları arasında veri kopyalama](data-factory-data-movement-activities.md#global) bölümünde desteklenen değerler ve daha fazla ayrıntı için.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Ayrıca, kaynak veri kümesi sütunları havuz veri kümesi kopyalama etkinliği tanımındaki sütunlarından yerine eşleyebilirsiniz. Ayrıntılar için bkz [Azure Data factory'de veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performans ve ayar
Bkz: [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md) veri taşıma (kopyalama etkinliği), en iyi duruma getirmek için çeşitli yollar ve performansı etkileyebilir anahtar Etkenler hakkında bilgi edinmek için.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

* [Kopyalama etkinliği Öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kopyalama etkinliği ile işlem hattı oluşturmaya yönelik adım adım yönergeler için.
