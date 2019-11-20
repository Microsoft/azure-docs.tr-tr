---
title: Verileri Azure tablosuna/konumundan taşıma
description: Azure Data Factory kullanarak verileri Azure Tablo depolama içine/altına taşımayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83f3a34a9b902b3a0e3b3ded34e36c8cbf50ed89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683067"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Azure Data Factory kullanarak verileri Azure tablosuna ve Azure 'a taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-azure-table-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-table-storage.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Azure Tablo depolama Bağlayıcısı](../connector-azure-table-storage.md).

Bu makalede, verileri Azure Tablo depolama alanına/konumundan taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur. 

Desteklenen herhangi bir kaynak veri deposundan verileri Azure Tablo depolama alanına veya Azure Tablo depolamadan desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak bir Azure Tablo depolama alanına/kaynağından veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz: 

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. 
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. 

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Azure Tablo depolamadan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları içeren örnekler için, bu makalenin [JSON örnekleri](#json-examples) bölümüne bakın.

Aşağıdaki bölümlerde, Azure Tablo depolama 'ya özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılar sağlanmaktadır: 

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Azure Blob depolamayı bir Azure Data Factory 'ye bağlamak için kullanabileceğiniz iki tür bağlı hizmet vardır. Bunlar: **Azurestorage** Linked Service ve **Azurestokıgesas** bağlı hizmeti. Azure depolama bağlı hizmeti, veri fabrikasına Azure depolama 'ya küresel erişim sağlar. Ancak, Azure Storage SAS (paylaşılan erişim Imzası) bağlı hizmeti, veri fabrikasını Azure depolama 'ya kısıtlı/zamana bağlı erişimle sağlar. Bu iki bağlı hizmet arasında başka farklılık yoktur. Gereksinimlerinize uygun olan bağlı hizmeti seçin. Aşağıdaki bölümler, bu iki bağlı hizmet hakkında daha fazla ayrıntı sağlar.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **AzureTable** türündeki veri kümesinin **typeproperties** bölümü aşağıdaki özelliklere sahiptir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Azure Tablo veritabanı örneğindeki tablonun adı. |Evet. Bir tableName, azureTableSourceQuery olmadan belirtildiğinde, tablodaki tüm kayıtlar hedefe kopyalanır. Bir azureTableSourceQuery belirtilmişse, sorguyu karşılayan tablodaki kayıtlar da hedefe kopyalanır. |

### <a name="schema-by-data-factory"></a>Data Factory şema
Azure tablosu gibi şema içermeyen veri depoları için Data Factory hizmeti şemayı aşağıdaki yollarla algılar:

1. Veri kümesi tanımında **Yapı** özelliğini kullanarak verilerin yapısını belirtirseniz, Data Factory hizmeti bu yapıyı şema olarak kabul eder. Bu durumda, bir satır bir sütun için değer içermiyorsa, için null değer sağlanır.
2. Veri kümesi tanımında **Yapı** özelliğini kullanarak verilerin yapısını belirtmezseniz, Data Factory verilerin ilk satırını kullanarak şemayı belirler. Bu durumda, ilk satır tam şemayı içermiyorsa, kopyalama işleminin sonucunda bazı sütunlar kaçırılacaktır.

Bu nedenle, şemaya ücretsiz veri kaynakları için en iyi yöntem, **Yapı** özelliğini kullanarak verilerin yapısını belirtmektir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış veri kümeleri ve ilkeler gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Diğer yandan etkinliğin typeProperties bölümünde bulunan özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

**AzureTableSource** , typeproperties bölümünde aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| azureTableSourceQuery |Verileri okumak için özel sorguyu kullanın. |Azure tablo sorgu dizesi. Sonraki bölümde örneklere bakın. |Hayır. Bir tableName, azureTableSourceQuery olmadan belirtildiğinde, tablodaki tüm kayıtlar hedefe kopyalanır. Bir azureTableSourceQuery belirtilmişse, sorguyu karşılayan tablodaki kayıtlar da hedefe kopyalanır. |
| azureTableSourceIgnoreTableNotFound |Swallow tablosunun özel durumunun mevcut olup olmadığını belirtin. |DEĞERI<br/>YANLÝÞ |Hayır |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery örnekleri
Azure Tablo sütunu dize türünde ise:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Azure Tablo sütunu tarih saat türünde ise:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** , typeproperties bölümünde aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Havuz tarafından kullanılabilen varsayılan bölüm anahtarı değeri. |Bir dize değeri. |Hayır |
| azureTablePartitionKeyName |Değerleri bölüm anahtarları olarak kullanılan sütunun adını belirtin. Belirtilmemişse, bölüm anahtarı olarak AzureTableDefaultPartitionKeyValue kullanılır. |Bir sütun adı. |Hayır |
| azureTableRowKeyName |Sütun değerleri satır anahtarı olarak kullanılan sütunun adını belirtin. Belirtilmezse, her satır için bir GUID kullanın. |Bir sütun adı. |Hayır |
| azureTableInsertType |Azure tablosuna veri ekleme modu.<br/><br/>Bu özellik, çıkış tablosunda eşleşen bölüm ve satır anahtarlarının değerlerinin değiştirilmesini veya birleştirildiğini denetler. <br/><br/>Bu ayarların (birleştirme ve değiştirme) nasıl çalıştığını öğrenmek için bkz. [varlık ekleme veya birleştirme](https://msdn.microsoft.com/library/azure/hh452241.aspx) ve [varlık ekleme veya değiştirme](https://msdn.microsoft.com/library/azure/hh452242.aspx) konuları. <br/><br> Bu ayar tablo düzeyinde değil, satır düzeyinde uygulanır ve hiçbir seçenek, girişte bulunmayan çıkış tablosundaki satırları silmez. |Birleştir (varsayılan)<br/>değiştirin |Hayır |
| writeBatchSize |WriteBatchSize veya writeBatchTimeout değeri isabet edildiğinde Azure tablosuna veri ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| writeBatchTimeout |WriteBatchSize veya writeBatchTimeout değeri isabet edildiğinde Azure tablosuna veri ekler |TimeSpan<br/><br/>Örnek: "00:20:00" (20 dakika) |Hayır (varsayılan olarak, depolama istemcisi varsayılan zaman aşımı değeri 90 sn) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Hedef sütunu azureTablePartitionKeyName olarak kullanabilmeniz için önce Translator JSON özelliğini kullanarak bir kaynak sütunu hedef sütuna eşleyin.

Aşağıdaki örnekte, DivisionID kaynak sütunu hedef sütunuyla eşlenir: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID, bölüm anahtarı olarak belirtilir.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON örnekleri
Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Azure Tablo depolama ve Azure Blob veritabanına verilerin nasıl kopyalanacağını gösterir. Ancak, veriler herhangi bir kaynaktan, desteklenen herhangi bir havuza **doğrudan** kopyalanabilir. Daha fazla bilgi için [kopyalama etkinliğini kullanarak verileri taşıma](data-factory-data-movement-activities.md)konusunun "desteklenen veri depoları ve biçimleri" bölümüne bakın.

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Örnek: Azure tablosundan Azure Blob 'a veri kopyalama
Aşağıdaki örnek şunu gösterir:

1. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties) türünde bağlı bir hizmet (her iki tablo & blobu için kullanılır).
2. [AzureTable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
4. AzureTableSource ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip işlem [hattı](data-factory-create-pipelines.md) .

Örnek, bir Azure tablosundaki Varsayılan bölüme ait verileri her saat blob 'una kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure depolama bağlı hizmeti:**

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
Azure Data Factory, iki tür Azure Storage bağlı hizmetini destekler: **Azurestorage** ve **Azurestorampasas**. Birincisi için, hesap anahtarını ve sonraki bir sürümü içeren bağlantı dizesini belirtirsiniz, paylaşılan erişim Imzası (SAS) URI 'sini belirtirsiniz. Ayrıntılar için [bağlı hizmetler](#linked-service-properties) bölümüne bakın.  

**Azure tablo giriş veri kümesi:**

Örnek, Azure tablosunda bir "MyTable" tablosu oluşturduğunuzu varsayar.

"External": "true" ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**AzureTableSource ve BlobSink ile işlem hattındaki etkinliği kopyalama:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **AzureTableSource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **AzureTableSourceQuery** özelliği Ile belirtilen SQL sorgusu, her saat kopyalama için varsayılan bölümden verileri seçer.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                    },
                    "sink": {
                        "type": "BlobSink"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Örnek: Azure Blobundan Azure tablosuna veri kopyalama
Aşağıdaki örnek şunu gösterir:

1. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties) türünde bağlı bir hizmet (her iki tablo & blob 'u için kullanılır)
2. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
3. [AzureTable](#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
4. [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) ve [AzureTableSink](#copy-activity-properties)kullanan kopyalama etkinliğine sahip işlem [hattı](data-factory-create-pipelines.md) .

Örnek, saat serisi verilerini bir Azure blobundan saatlik bir Azure tablosuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure depolama (Azure Tablo & blob) bağlı hizmeti için:**

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

Azure Data Factory, iki tür Azure Storage bağlı hizmetini destekler: **Azurestorage** ve **Azurestorampasas**. Birincisi için, hesap anahtarını ve sonraki bir sürümü içeren bağlantı dizesini belirtirsiniz, paylaşılan erişim Imzası (SAS) URI 'sini belirtirsiniz. Ayrıntılar için [bağlı hizmetler](#linked-service-properties) bölümüne bakın.

**Azure Blob giriş veri kümesi:**

Veriler her saat yeni bir bloba alınır (sıklık: saat, Aralık: 1). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün bölümünü ve dosya adını kullanır başlangıç zamanının saat kısmını kullanır. "External": "true" ayarı, Data Factory hizmetine veri kümesinin dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini bildirir.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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

**Azure Tablo çıkış veri kümesi:**

Örnek, verileri Azure tablosu 'nda "MyTable" adlı bir tabloya kopyalar. Blob CSV dosyasının içermesini beklediğinizi aynı sayıda sütuna sahip bir Azure tablosu oluşturun. Yeni satırlar tabloya her saat eklenir.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**BlobSource ve AzureTableSink ile işlem hattındaki etkinliği kopyalama:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **AzureTableSink**olarak ayarlanır.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Azure tablosu için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi, kopyalama etkinliği, kaynak türlerindeki otomatik tür dönüştürmeleri aşağıdaki iki adımlı yaklaşımla birlikte havuz türlerine uygular.

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Verileri Azure tablosundan & taşırken Azure Tablo [hizmeti tarafından tanımlanan aşağıdaki eşlemeler](https://msdn.microsoft.com/library/azure/dd179338.aspx) , Azure Tablo OData türlerinden .net türüne ve tam tersi şekilde kullanılır.

| OData veri türü | .NET türü | Ayrıntılar |
| --- | --- | --- |
| EDM.Binary |Byte [] |64 KB 'a kadar olan bir bayt dizisi. |
| Edm.Boolean |bool |Boolean değeri. |
| EDM. DateTime |DateTime |Eşgüdümlü Evrensel Saat (UTC) olarak ifade edilen 64 bitlik bir değer. Desteklenen tarih saat aralığı 12:00 gece yarısı başlar, 1 Ocak 1601 M.S. (C.E.), UTC. Aralık 31 Aralık 9999 ' de sona erer. |
| Edm.Double |double |64 bitlik kayan nokta değeri. |
| EDM.Guid |Guid |128 bitlik bir genel benzersiz tanımlayıcı. |
| Edm.Int32 |Int32 |32 bitlik bir tamsayı. |
| Edm.Int64 |Int64 |64 bitlik bir tamsayı. |
| Edm.String |Dize |UTF-16 kodlu bir değer. Dize değerleri en fazla 64 KB olabilir. |

### <a name="type-conversion-sample"></a>Tür dönüştürme örneği
Aşağıdaki örnek, Azure Blobundan Azure tablosuna tür dönüştürmeleri ile veri kopyalama içindir.

Blob veri kümesinin CSV biçiminde olduğunu ve üç sütun içerdiğini varsayın. Bunlardan biri, haftanın günü için kısaltılmış Fransız adlarını kullanan özel bir tarih saat biçimindeki bir tarih saat sütunudur.

Blob kaynak veri kümesini, sütunlar için tür tanımlarıyla birlikte aşağıdaki gibi tanımlayın.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Azure Tablo OData türünden .NET türüne tür eşlemesi verildiğinde, tabloyu Azure tablosu 'nda aşağıdaki şemayla tanımlarsınız.

**Azure Tablo şeması:**

| Sütun adı | Tür |
| --- | --- |
| UserID |Edm.Int64 |
| ad |Edm.String |
| LastLoginDate |EDM. DateTime |

Ardından, Azure Tablo veri kümesini aşağıdaki şekilde tanımlayın. Tür bilgileri temel alınan veri deposunda zaten belirtildiğinden bu yana tür bilgileriyle "yapı" bölümünü belirtmeniz gerekmez.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Bu durumda Data Factory otomatik olarak, blob 'dan Azure tablosuna veri taşırken "fr-fr" kültürünü kullanarak tarih saat alanı içeren özel tarih saat biçimindeki tür dönüştürmelerini otomatik olarak yapar.

> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Azure Data Factory ve en iyileştirmek için çeşitli yollarla veri taşıma (kopyalama etkinliği) performansını etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [kopyalama etkinliği performansı & ayarlama Kılavuzu](data-factory-copy-activity-performance.md).
