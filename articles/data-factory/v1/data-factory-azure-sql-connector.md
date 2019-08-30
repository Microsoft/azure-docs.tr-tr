---
title: Veri kopyalama/Azure SQL veritabanı'ndan | Microsoft Docs
description: Azure Data Factory kullanarak Azure SQL veritabanı/deposundan veri kopyalamayı öğreneceksiniz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: efc7e255a8b3644d8b95bf461e49cd67b2219676
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839519"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL veritabanı ve veri kopyalamak
> [!div class="op_single_selector" title1="Data Factory hizmetinin kullandığınız sürümü seçin:"]
> * [Sürüm 1](data-factory-azure-sql-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-sql-database.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz [V2'de Azure SQL Veritabanı Bağlayıcısı](../connector-azure-sql-database.md).

Bu makalede, için ve Azure SQL veritabanı'ndan veri taşımak için Azure Data Factory kopyalama etkinliği kullanmayı açıklar. Yapılar [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi, kopyalama etkinliği ile verileri taşıma genel bir bakış sunar.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
Veri kopyalayabilirsiniz **Azure SQL veritabanı'ndan** aşağıdaki verilere depolar:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarından veri kopyalayabilirsiniz **Azure SQL veritabanı**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Desteklenen kimlik doğrulama türü
Azure SQL Veritabanı Bağlayıcısı, temel kimlik doğrulamasını destekler.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API'lerini kullanarak veri gönderip buralardan veri bir Azure SQL veritabanına taşıyan kopyalama etkinliği ile işlem hattı oluşturabilirsiniz.

Bir işlem hattı oluşturmanın en kolay yolu kullanmaktır **Kopyalama Sihirbazı'nı**. Bkz: [Öğreticisi: Kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) veri kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma hızlı bir kılavuz.

Ayrıca, bir işlem hattı oluşturmak için aşağıdaki araçları kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**, ve **REST API**. Bkz: [kopyalama etkinliği Öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kopyalama etkinliği ile işlem hattı oluşturmak adım adım yönergeler için.

API'ler ve Araçlar kullanmanıza bakılmaksızın, bir havuz veri deposu için bir kaynak veri deposundan veri taşıyan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirin:

1. Oluşturma bir **veri fabrikası**. Veri fabrikası, bir veya daha fazla işlem hattı içerebilir.
2. Oluşturma **bağlı hizmetler** girdi ve çıktı verilerini bağlamak için veri fabrikanıza depolar. Örneğin, bir Azure SQL veritabanı için bir Azure blob depolamadan veri kopyalıyorsanız, Azure depolama hesabınızı ve Azure SQL veritabanına veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. Azure SQL veritabanı'na özgü bağlı hizmeti özellikleri için bkz: [bağlı hizmeti özellikleri](#linked-service-properties) bölümü.
3. Oluşturma **veri kümeleri** kopyalama işleminin girdi ve çıktı verilerini göstermek için. Son adımda bahsedilen örnekte, bir veri kümesi blob kapsayıcıyı ve girdi verilerini içeren klasörü belirtin oluşturun. Ayrıca, blob depolama alanından kopyalanan verileri tutan Azure SQL veritabanındaki SQL tablosunu belirtirsiniz. başka bir veri kümesi oluşturursunuz. Azure Data Lake Store için özel veri kümesi özellikleri için bkz: [veri kümesi özellikleri](#dataset-properties) bölümü.
4. Oluşturma bir **işlem hattı** bir veri kümesini girdi ve çıktı olarak bir veri kümesini alan kopyalama etkinliği ile. Daha önce bahsedilen örnekte BlobSource bir kaynak ve SqlSink havuz olarak kopyalama etkinliği için kullanırsınız. Azure SQL veritabanı'ndan Azure Blob depolama alanına kopyalanıyorsa, benzer şekilde, SqlSource ve BlobSink kopyalama etkinliği kullanırsınız. Azure SQL veritabanı'na özel kopyalama etkinliği özellikleri için bkz: [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir kaynak veya havuz bir veri deposunu kullanma hakkında daha fazla ayrıntı için önceki bölümde veri deponuz için bağlantıya tıklayın.

Sihirbazı'nı kullandığınızda, bu Data Factory varlıklarını (bağlı hizmetler, veri kümeleri ve işlem hattı) için JSON tanımları sizin için otomatik olarak oluşturulur. Araçlar/API'leri (dışında .NET API'si) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlayın. Azure SQL veritabanı/veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları ile örnekleri için bkz [JSON örnekler](#json-examples-for-copying-data-to-and-from-sql-database) bu makalenin.

Aşağıdaki bölümler, Azure SQL veritabanı'na Data Factory varlıklarını belirli tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri
Bir Azure SQL kullanarak Azure SQL veritabanına veri fabrikanıza bağlı hizmeti. Aşağıdaki tabloda, Azure SQL bağlı hizmeti için özel JSON öğeleri için bir açıklama sağlar.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği ayarlanmalıdır: **AzureSqlDatabase** |Evet |
| connectionString |ConnectionString özelliği için Azure SQL veritabanı örneğine bağlanmak için gereken bilgileri belirtin. Temel kimlik doğrulaması desteklenir. |Evet |

> [!IMPORTANT]
> Yapılandırma [Azure SQL veritabanı Güvenlik Duvarı](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) veritabanı sunucusuna [Azure hizmetlerinin sunucuya erişmesine izin](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Dış Azure data factory ağ geçidi ile şirket içi veri kaynaklarından dahil olmak üzere Azure SQL veritabanı'na veri kopyalıyorsanız, ayrıca, Azure SQL veritabanı'na veri gönderiyor makine için uygun IP adresi aralığı yapılandırın.

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Bir Azure SQL veritabanı'nda girdi ve çıktı verilerini temsil eden bir veri kümesi belirtmek için veri kümesine öğesinin type özelliği ayarlayın: **AzureSqlTable**. Ayarlama **linkedServiceName** özellik kümesinin adı olarak Azure SQL bağlı hizmeti.

Bölümleri ve veri kümeleri tanımlamak için kullanılabilir özellikleri tam listesi için bkz [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bölümler bir veri kümesi JSON İlkesi yapısı ve kullanılabilirlik gibi tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablo, vs.) için benzer.

TypeProperties bölümünün her tür veri kümesi için farklıdır ve verilerin veri deposundaki konumu hakkında bilgi sağlar. **TypeProperties** türü için veri kümesi bölümünü **AzureSqlTable** aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Tablo veya Görünüm bağlı hizmeti Azure SQL veritabanı örneğinde başvurduğu adı. |Evet |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Bölümleri & etkinlikleri tanımlamak için mevcut özelliklerin tam listesi için bkz: [işlem hatları oluşturma](data-factory-create-pipelines.md) makalesi. İlke adı ve açıklaması, girdi ve çıktı tabloları gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği, tek bir girdi alır ve tek bir çıktı üretir.

Diğer yandan bulunan özelliklerin **typeProperties** etkinlik bölümünü her etkinlik türü ile farklılık gösterir. Kopyalama etkinliği için kaynaklar ve havuzlar türlerine bağlı olarak farklılık gösterir.

Bir Azure SQL veritabanı'ndan veri taşıyorsanız, kaynak türü için kopyalama etkinliğindeki ayarladığınız **SqlSource**. Bir Azure SQL veritabanına veri taşıyorsanız, benzer şekilde, Havuz türü için kopyalama etkinliğindeki ayarladığınız **SqlSink**. Bu bölümde SqlSource ve SqlSink tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sqlsource"></a>SqlSource
Kopya etkinlikteki kaynak türünde olduğunda **SqlSource**, aşağıdaki özellikler kullanılabilir **typeProperties** bölümü:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorgu kullanın. |SQL sorgu dizesi. Örnek: `select * from MyTable`. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablo verilerini okuyan saklı yordamın adı. |Saklı yordamın adı. Son SQL deyim bir SELECT deyimi saklı yordam içinde olmalıdır. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Adları ve parametreleri büyük küçük harfleri, adları ve saklı yordam parametreleri büyük küçük harfleri eşleşmelidir. |Hayır |

Varsa **sqlReaderQuery** belirtilen SqlSource için kopyalama etkinliği, verileri almak için Azure SQL veritabanı kaynak karşı bu sorgu çalıştırır. Alternatif olarak, bir saklı yordam belirterek belirtebileceğiniz **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordamın parametreleri sürerse).

SqlReaderQuery ya da sqlReaderStoredProcedureName belirtmezseniz JSON veri kümesi yapısı bölümünde tanımlanan sütunları bir sorgu oluşturmak için kullanılır (`select column1, column2 from mytable`) için Azure SQL veritabanınızda çalıştırın. Veri kümesi tanımı yapısına sahip değilse, tüm sütunları tablodan seçilir.

> [!NOTE]
> Kullanırken **sqlReaderStoredProcedureName**, yine de için bir değer belirtmeniz gerekiyorsa **tableName** veri kümesi JSON özelliğinde. Ancak bu tabloya karşı gerçekleştirilen başka bir doğrulama vardır.
>
>

### <a name="sqlsource-example"></a>SqlSource örneği

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Saklı yordam tanımı:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| writeBatchTimeout |Toplu ekleme işlemi zaman aşımına uğramadan önce tamamlanması için bir süre bekleyin. |TimeSpan<br/><br/> Örnek: "00: 30:00" (30 dakika). |Hayır |
| writeBatchSize |Arabellek boyutu writeBatchSize ulaştığında veri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| sqlWriterCleanupScript |Belirli bir dilimin veri Temizlenen şekilde yürütmek kopyalama etkinliği için bir sorgu belirtin. Daha fazla bilgi için [tekrarlanabilir kopyalama](#repeatable-copy). |Bir sorgu deyimi. |Hayır |
| sliceIdentifierColumnName |Kopyalama etkinliği'nin ne zaman yeniden çalıştırılacağını belirli bir dilimin verileri temizlemek için kullanılan otomatik dilim tanımlayıcısı ile doldurmak için bir sütun adı belirtin. Daha fazla bilgi için [tekrarlanabilir kopyalama](#repeatable-copy). |Bir sütunun veri türüyle binary(32) sütun adı. |Hayır |
| sqlWriterStoredProcedureName |Kaynak verileri hedef tabloya örn upsert eder ya da kendi iş mantığınızı kullanarak dönüşüm nasıl uygulanacağını tanımlayan saklı yordamın adı. <br/><br/>Bu saklı yordamı olacaktır Not **toplu iş çağrılan**. Yalnızca bir kez çalışır ve hiçbir kaynak verileri ile/delete örn truncate yapmak için kullanma işlemi yapmak istiyorsanız `sqlWriterCleanupScript` özelliği. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Adları ve parametreleri büyük küçük harfleri, adları ve saklı yordam parametreleri büyük küçük harfleri eşleşmelidir. |Hayır |
| sqlWriterTableType |Saklı yordam, kullanılacak bir tablo türü adı belirtin. Kopyalama etkinliği, taşınan veri bir geçici tablo bu tablo türü ile kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle kopyalanan verileri birleştirebilirsiniz. |Bir tablo türü adı. |Hayır |

#### <a name="sqlsink-example"></a>SqlSink örneği

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON örnekler ve SQL veritabanı'ndan veri kopyalamak için
Aşağıdaki örnekler kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımları sağlamak [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Bunlar, Azure SQL Database ve Azure Blob Depolama ve veri kopyalamak nasıl gösterir. Ancak, veriler kopyalanabilir **doğrudan** herhangi birinden herhangi birine belirtilen havuzlarını kaynakları [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopyalama etkinliğini kullanarak Azure Data Factory'de.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Örnek: Verileri Azure SQL veritabanı'ndan Azure Blob kopyalama
Aynı aşağıdaki Data Factory varlıkları tanımlar:

1. Bağlı hizmet türü [AzureSqlDatabase](#linked-service-properties).
2. Bağlı hizmet türü [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [AzureSqlTable](#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) türü [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [işlem hattı](data-factory-create-pipelines.md) kullanan bir kopyalama etkinlikli [SqlSource](#copy-activity-properties) ve [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Örnek zaman serisi verileri (saatlik, günlük, vb.) Azure SQL veritabanındaki bir tabloda bir bloba saatte kopyalar. Bu örneklerde kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

**Azure SQL veritabanı bağlı hizmeti:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Bu bağlı hizmeti tarafından desteklenen özelliklerin listesi için Azure SQL bağlı hizmeti bölümüne bakın.

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
Bkz: [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) bu bağlı hizmeti tarafından desteklenen özelliklerin listesi için makale.


**Azure SQL giriş veri kümesi:**

Örnek, "MyTable" Azure SQL'de bir tablo oluşturdunuz ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiği varsayılır.

"Dış" ayarını: "true" bildirir Azure Data Factory hizmetinin veri kümesi dış veri fabrikasına ve veri fabrikasında bir etkinliği tarafından üretilen değil.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

Azure SQL veri kümesi türü özellikler bölümü listesi için bu veri kümesi türü tarafından desteklenen özelliklerin bakın.

**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (Sıklık: saat, interval: 1). Blob için klasör yolu işlenmekte olan dilimin başlangıç zamanı temel alınarak dinamik olarak değerlendirilir. Yıl, ay, gün ve saat bölümlerini başlangıç zamanı klasör yolu kullanır.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Bkz: [Azure Blob veri kümesi türü özellikleri](data-factory-azure-blob-connector.md#dataset-properties) bu veri kümesi türü tarafından desteklenen özellikler bölümünü listesi.

**SQL kaynak ve havuz Blob ile bir işlem hattındaki kopyalama etkinliği:**

İşlem hattının giriş ve çıkış veri kümelerini kullanmak için yapılandırıldığı ve saatte bir çalışacak şekilde zamanlanmış bir kopyalama etkinliği içeriyor. JSON tanımı, işlem hattındaki **kaynak** türü ayarlandığında **SqlSource** ve **havuz** türü ayarlandığında **BlobSink**. SQL sorgusu için belirtilen **SqlReaderQuery** özelliği veri kopyalamak için son bir saat içinde seçer.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
Örnekte, **sqlReaderQuery** SqlSource için belirtilir. Kopyalama etkinliği, verileri almak için Azure SQL veritabanı kaynak karşı bu sorguyu çalıştırır. Alternatif olarak, bir saklı yordam belirterek belirtebileceğiniz **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordamın parametreleri sürerse).

SqlReaderQuery ya da sqlReaderStoredProcedureName belirtmezseniz JSON veri kümesi yapısı bölümünde tanımlanan sütunları Azure SQL veritabanında çalıştırmak için bir sorgu oluşturmak için kullanılır. Örneğin: `select column1, column2 from mytable` Veri kümesi tanımı yapısına sahip değilse, tüm sütunları tablodan seçilir.

Bkz: [Sql kaynağı](#sqlsource) bölümü ve [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSource ve BlobSink tarafından desteklenen özelliklerin listesi için.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Örnek: Verileri Azure Blobundan Azure SQL veritabanına kopyalama
Örnek, aşağıdaki Data Factory varlıkları tanımlar:

1. Bağlı hizmet türü [AzureSqlDatabase](#linked-service-properties).
2. Bağlı hizmet türü [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) türü [AzureSqlTable](#dataset-properties).
5. A [işlem hattı](data-factory-create-pipelines.md) kullanan kopyalama etkinlikli [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) ve [SqlSink](#copy-activity-properties).

Zaman serisi, Azure SQL tablosuna (saatlik, günlük, vb.) verileri Azure blob örnek kopya saatte veritabanı. Bu örneklerde kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

**Azure SQL bağlı hizmeti:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Bu bağlı hizmeti tarafından desteklenen özelliklerin listesi için Azure SQL bağlı hizmeti bölümüne bakın.

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
Bkz: [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) bu bağlı hizmeti tarafından desteklenen özelliklerin listesi için makale.


**Azure Blob girdi veri kümesi:**

Veri alındığından yeni blobundan her saat (Sıklık: saat, interval: 1). Blob klasörü yolu ve dosya adı dinamik olarak değerlendirilir işlenmekte olan dilimin başlangıç zamanı temel alınarak. Klasör yolu yıl, ay ve gün kısmını başlangıç saati ve dosya adı başlangıç zamanı saat bölümünü kullanır. "dış": "true" ayarı, bu tablo harici veri fabrikasına ve veri fabrikasında bir etkinliği tarafından üretilen değil Data Factory hizmetinin bildirir.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
Bkz: [Azure Blob veri kümesi türü özellikleri](data-factory-azure-blob-connector.md#dataset-properties) bu veri kümesi türü tarafından desteklenen özellikler bölümünü listesi.

**Azure SQL veritabanı çıktı veri kümesi:**

Örnek verileri Azure SQL "MyTable" adlı bir tabloya kopyalar. Blob CSV dosyasını içerecek şekilde beklediğiniz gibi aynı sayıda sütun ile Azure SQL tablosu oluşturun. Yeni satırlar saatte tablosuna eklenir.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Azure SQL veri kümesi türü özellikler bölümü listesi için bu veri kümesi türü tarafından desteklenen özelliklerin bakın.

**Blob kaynağı ve SQL ile bir işlem hattındaki kopyalama etkinliği havuzu:**

İşlem hattının giriş ve çıkış veri kümelerini kullanmak için yapılandırıldığı ve saatte bir çalışacak şekilde zamanlanmış bir kopyalama etkinliği içeriyor. JSON tanımı, işlem hattındaki **kaynak** türü ayarlandığında **BlobSource** ve **havuz** türü ayarlandığında **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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
Bkz: [Sql havuz](#sqlsink) bölümü ve [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSink ve BlobSource tarafından desteklenen özelliklerin listesi için.

## <a name="identity-columns-in-the-target-database"></a>Hedef veritabanındaki Kimlik sütunları
Bu bölümde, verileri bir kimlik sütunu olmayan bir kaynak tablosundan IDENTITY sütunu hedef tabloya kopyalamak için bir örnek sağlar.

**Kaynak Tablo:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Hedef Tablo:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Hedef tablo bir kimlik sütunu olduğuna dikkat edin.

**Kaynak veri kümesi JSON tanımı**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Hedef veri kümesi JSON tanımı**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Kaynak ve hedef tablonuz olarak farklı bir şemaya sahip olduğuna dikkat edin (hedef ek bir sütun kimliği içeriyor). Bu senaryoda, belirtmeniz gerekir. **yapısı** kimlik sütunu içermeyen hedef veri kümesi tanımında özelliği.

## <a name="invoke-stored-procedure-from-sql-sink"></a>SQL havuz saklı yordam çağırma
Bir işlem hattının kopyalama etkinliği havuz SQL saklı bir yordam çağırma örneği için bkz: [kopyalama etkinliğindeki SQL havuz için saklı yordam çağırma](data-factory-invoke-stored-procedure-from-copy-activity.md) makalesi.

## <a name="type-mapping-for-azure-sql-database"></a>Azure SQL veritabanı için tür eşlemesi
Belirtildiği gibi [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makale kopyalama etkinliği kaynak türünden aşağıdaki 2 adımlı yaklaşım türleriyle havuz otomatik tür dönüştürmeleri gerçekleştirir:

1. Yerel kaynak türlerinden .NET türüne dönüştürün
2. .NET türünden yerel havuz türüne dönüştürün

Veri taşımak ve Azure SQL veritabanı'ndan, aşağıdaki eşlemeler SQL türü .NET türü ve bunun tersi de kullanılır. Eşleme, aynı SQL Server veri türü eşlemesi ADO.NET için bir.

| SQL Server veritabanı altyapısı türü | .NET framework türü |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| metin |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Sütunları havuz için kaynak eşlemesi
Kaynak veri kümesindeki sütunları havuz veri kümesi için eşleme sütunları hakkında bilgi edinmek için bkz. [Azure Data factory'de veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Tekrarlanabilir kopyalama
SQL Server veritabanına veri kopyalama, kopyalama etkinliği havuz tabloya verileri varsayılan olarak ekler. Bunun yerine bir UPSERT gerçekleştirmek için bkz: [Repeatable yazmak için SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) makalesi.

İlişkisel veri kopyalama verileri depoladığında yinelenebilirliği istenmeyen sonuçlar önlemek için göz önünde bulundurun. Azure Data Factory'de bir dilim el ile çalıştırabilirsiniz. Bir hata oluştuğunda bir dilimi yeniden çalıştırmak için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki yolla yeniden çalıştırıldığında, aynı veri dilimi çalıştırılan kaç kez olursa olsun okuma emin olmanız gerekir. Bkz: [ilişkisel kaynaktan okumak Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Bkz: [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md) veri taşıma (kopyalama etkinliği) Azure Data Factory ve bunu en iyi duruma getirmek için çeşitli yollar, performansı etkileyebilir anahtar Etkenler hakkında bilgi edinmek için.
