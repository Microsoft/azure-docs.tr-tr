---
title: Azure SQL veritabanına veri kopyalama
description: Azure Data Factory kullanarak Azure SQL veritabanına veri kopyalama hakkında bilgi edinin.
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
ms.openlocfilehash: 8d53d61991f191d2cd0636dba918e9499c4f5d2b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683083"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL veritabanına veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-azure-sql-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-sql-database.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Azure SQL Veritabanı Bağlayıcısı](../connector-azure-sql-database.md).

Bu makalede, Azure SQL veritabanına veri taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
**Azure SQL veritabanı 'ndan** aşağıdaki veri depolarına veri kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarından verileri **Azure SQL veritabanı 'na**kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Desteklenen kimlik doğrulama türü
Azure SQL Veritabanı Bağlayıcısı, temel kimlik doğrulamasını destekler.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak verileri Azure SQL veritabanına taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Bir **Veri Fabrikası**oluşturun. Bir veri fabrikası bir veya daha fazla işlem hattı içerebilir.
2. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun. Örneğin, bir Azure Blob depolama alanından Azure SQL veritabanına veri kopyalıyorsanız, Azure depolama hesabınızı ve Azure SQL veritabanınızı veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. Azure SQL veritabanı 'na özgü bağlantılı hizmet özellikleri için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.
3. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. Son adımda bahsedilen örnekte, blob kapsayıcısını ve girdi verilerini içeren klasörü belirtmek için bir veri kümesi oluşturursunuz. Ve, blob depolamadan kopyalanmış verileri tutan Azure SQL veritabanında SQL tablosunu belirtmek için başka bir veri kümesi oluşturursunuz. Azure Data Lake Store özgü veri kümesi özellikleri için bkz. [veri kümesi özellikleri](#dataset-properties) bölümü.
4. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. Daha önce bahsedilen örnekte, BlobSource değerini kaynak ve SqlSink olarak kopyalama etkinliği için havuz olarak kullanacaksınız. Benzer şekilde, Azure SQL veritabanından Azure Blob depolama alanına kopyalama yapıyorsanız, kopyalama etkinliğinde SqlSource ve BlobSink kullanın. Azure SQL veritabanı 'na özel kopyalama etkinliği özellikleri için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir veri deposunu kaynak veya havuz olarak kullanma hakkında ayrıntılı bilgi için, veri deponuzdaki önceki bölümde yer alan bağlantıya tıklayın.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Azure SQL veritabanına veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları içeren örnekler için, bu makalenin [JSON örnekleri](#json-examples-for-copying-data-to-and-from-sql-database) bölümüne bakın.

Aşağıdaki bölümler, Azure SQL veritabanı 'na özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Azure SQL bağlı hizmeti, bir Azure SQL veritabanını veri fabrikanıza bağlar. Aşağıdaki tabloda, Azure SQL bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **Azuressqldatabase** olarak ayarlanmalıdır |Evet |
| connectionString |ConnectionString özelliği için Azure SQL veritabanı örneğine bağlanmak için gereken bilgileri belirtin. Yalnızca temel kimlik doğrulaması desteklenir. |Evet |

> [!IMPORTANT]
> Azure [hizmetlerinin sunucuya erişmesine izin](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)vermek için veritabanı sunucusunu [Azure SQL veritabanı güvenlik duvarını](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) yapılandırın. Ayrıca, Data Factory ağ geçidine sahip şirket içi veri kaynaklarından Azure SQL veritabanı 'na veri kopyalıyorsanız, verileri Azure SQL veritabanına gönderen makine için uygun IP adresi aralığını yapılandırın.

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Bir Azure SQL veritabanında giriş veya çıkış verilerini temsil edecek bir veri kümesi belirtmek için, veri kümesinin Type özelliğini: **Azurestabtable**olarak ayarlarsınız. Veri kümesinin **Linkedservicename** ÖZELLIĞINI Azure SQL bağlı hizmetinin adı olarak ayarlayın.

Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Azurestabtable** türündeki veri kümesinin **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Azure SQL veritabanı örneğindeki tablonun veya görünümün adı. |Evet |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği yalnızca bir girdi alır ve yalnızca bir çıktı üretir.

Ancak, etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Verileri bir Azure SQL veritabanından taşıyorsanız, kopyalama etkinliğindeki kaynak türünü **SQLSource**olarak ayarlarsınız. Benzer şekilde, verileri bir Azure SQL veritabanına taşıyorsanız, kopyalama etkinliğindeki havuz türünü **Sqlsink**olarak ayarlarsınız. Bu bölüm, SqlSource ve SqlSink tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sqlsource"></a>SqlSource
Kopyalama etkinliğinde, kaynak **SQLSource**türünde olduğunda, **typeproperties** bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örnek: `select * from MyTable`. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablodaki verileri okuyan saklı yordamın adı. |Saklı yordamın adı. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |

SqlSource için **Sqlreaderquery** belirtilmişse kopyalama etkinliği, verileri almak IÇIN Azure SQL veritabanı kaynağına karşı bu sorguyu çalıştırır. Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, JSON veri kümesinin yapı bölümünde tanımlanan sütunlar, Azure SQL veritabanında çalıştırılacak bir sorgu (`select column1, column2 from mytable`) oluşturmak için kullanılır. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

> [!NOTE]
> **SqlReaderStoredProcedureName**kullandığınızda JSON veri kümesindeki **TableName** özelliği için de bir değer belirtmeniz gerekir. Ancak bu tabloda gerçekleştirilen hiçbir doğrulama yoktur.
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
**Sqlsink** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |
| writeBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| sqlWriterCleanupScript |Bir kopyalama etkinliğinin yürütülmesi için belirli bir dilim verilerinin temizlenmesi için bir sorgu belirtin. Daha fazla bilgi için bkz. [yinelenebilir kopya](#repeatable-copy). |Sorgu ekstresi. |Hayır |
| Dilimleyiceıdentifiercolumnname |Kopyalama etkinliği için, yeniden çalıştırıldığında belirli bir dilimin verilerini temizlemek için kullanılan otomatik olarak oluşturulan dilim tanımlayıcısı ile doldurulacak bir sütun adı belirtin. Daha fazla bilgi için bkz. [yinelenebilir kopya](#repeatable-copy). |Binary (32) veri türüne sahip bir sütunun sütun adı. |Hayır |
| sqlWriterStoredProcedureName |Hedef tabloya kaynak verilerinin nasıl uygulanacağını tanımlayan saklı yordamın adı (örneğin, kendi iş mantığınızı kullanarak). <br/><br/>Bu saklı yordamın **toplu iş başına çağrılacağını**aklınızda görürsünüz. Yalnızca bir kez çalıştırılan ve kaynak verilerle hiçbir şey olmayan bir işlem yapmak istiyorsanız, `sqlWriterCleanupScript` özelliğini kullanın. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |
| sqlWriterTableType |Saklı yordamda kullanılacak bir tablo türü adı belirtin. Kopyalama etkinliği, verileri bu tablo türüyle geçici bir tabloda kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle Kopyalanmakta olan verileri birleştirebilirler. |Tablo türü adı. |Hayır |

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>SQL veritabanına veri kopyalanmaya yönelik JSON örnekleri
Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Azure SQL veritabanı ve Azure Blob depolama 'ya veri kopyalamayı gösterir. Ancak, veriler, Azure Data Factory ' deki kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir kaynaktan herhangi bir kaynağa **doğrudan** kopyalanabilir.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Örnek: Azure SQL veritabanından Azure Blob 'a veri kopyalama
Aynı şekilde, aşağıdaki Data Factory varlıkları tanımlanmaktadır:

1. [Azuressqldatabase](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Azuressqltable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [SQLSource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, zaman serisi verilerini (saatlik, günlük, vb.) Azure SQL veritabanındaki bir tablodan her saat için bir bloba kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

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
Bu bağlı hizmet tarafından desteklenen özelliklerin listesi için bkz. Azure SQL bağlı hizmeti bölümü.

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
Bu bağlı hizmet tarafından desteklenen özelliklerin listesi için bkz. [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) makalesi.


**Azure SQL giriş veri kümesi:**

Örnek, Azure SQL 'de bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiğini varsayar.

"External": "true" ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Azure Data Factory hizmetine bildirir.

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

Bu veri kümesi türü tarafından desteklenen özelliklerin listesi için bkz. Azure SQL veri kümesi türü özellikleri bölümü.

**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

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
Bu veri kümesi türü tarafından desteklenen özelliklerin listesi için bkz. [Azure blob veri kümesi türü özellikleri](data-factory-azure-blob-connector.md#dataset-properties) bölümü.

**SQL kaynağı ve BLOB havuzu içeren bir işlem hattındaki kopyalama etkinliği:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **SQLSource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sqlreaderquery** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

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
Örnekte, SqlSource için **Sqlreaderquery** belirtilir. Kopyalama etkinliği, verileri almak için Azure SQL veritabanı kaynağına karşı bu sorguyu çalıştırır. Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, JSON veri kümesinin yapı bölümünde tanımlanan sütunlar, Azure SQL veritabanında çalıştırılacak bir sorgu oluşturmak için kullanılır. Örneğin: `select column1, column2 from mytable`. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

SqlSource ve BlobSink tarafından desteklenen özelliklerin listesi için bkz. [SQL kaynak](#sqlsource) bölümü ve [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) .

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Örnek: Azure Blobundan Azure SQL veritabanı 'na veri kopyalama
Örnek, aşağıdaki Data Factory varlıklarını tanımlar:

1. [Azuressqldatabase](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [Azuressqltable](#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) ve [Sqlsink](#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, zaman serisi verilerini (saatlik, günlük, vb.) Azure blobundan her saat Azure SQL veritabanı 'ndaki bir tabloya kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

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
Bu bağlı hizmet tarafından desteklenen özelliklerin listesi için bkz. Azure SQL bağlı hizmeti bölümü.

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
Bu bağlı hizmet tarafından desteklenen özelliklerin listesi için bkz. [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) makalesi.


**Azure Blob giriş veri kümesi:**

Veriler her saat yeni bir bloba alınır (sıklık: saat, Aralık: 1). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün bölümünü ve dosya adını kullanır başlangıç zamanının saat kısmını kullanır. "External": "true" ayarı, bu tablonun veri fabrikasında dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

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
Bu veri kümesi türü tarafından desteklenen özelliklerin listesi için bkz. [Azure blob veri kümesi türü özellikleri](data-factory-azure-blob-connector.md#dataset-properties) bölümü.

**Azure SQL veritabanı çıkış veri kümesi:**

Örnek, verileri Azure SQL 'de "MyTable" adlı bir tabloya kopyalar. Blob CSV dosyasının içermesini bekleyen aynı sayıda sütunla Azure SQL 'de tablo oluşturun. Yeni satırlar tabloya her saat eklenir.

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
Bu veri kümesi türü tarafından desteklenen özelliklerin listesi için bkz. Azure SQL veri kümesi türü özellikleri bölümü.

**Blob kaynağı ve SQL havuzu içeren bir işlem hattındaki kopyalama etkinliği:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **sqlsink**olarak ayarlanır.

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
SqlSink ve BlobSource tarafından desteklenen özelliklerin listesi için bkz. [SQL Sink](#sqlsink) bölümü ve [blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="identity-columns-in-the-target-database"></a>Hedef veritabanındaki kimlik sütunları
Bu bölüm, kimlik sütunu olmayan bir kaynak tablodaki verileri kimlik sütunu olan bir hedef tabloya kopyalamak için bir örnek sağlar.

**Kaynak tablo:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Hedef tablo:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Hedef tabloda bir kimlik sütunu olduğuna dikkat edin.

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

Kaynak ve hedef tablonuz farklı bir şemaya sahip olduğundan (hedef, kimliğe sahip ek bir sütuna sahiptir). Bu senaryoda, Identity sütununu içermeyen hedef veri kümesi tanımında **Yapı** özelliğini belirtmeniz gerekir.

## <a name="invoke-stored-procedure-from-sql-sink"></a>SQL havuzundan saklı yordam çağır
Bir işlem hattının kopyalama etkinliğinde SQL havuzundan saklı yordam çağırma örneği için, bkz. [kopyalama etkinliği 'NDE SQL havuzu için saklı yordamı çağırma](data-factory-invoke-stored-procedure-from-copy-activity.md) makalesi.

## <a name="type-mapping-for-azure-sql-database"></a>Azure SQL veritabanı için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi, kopyalama etkinliği, kaynak türlerinden aşağıdaki 2 adımlı yaklaşımla otomatik tür dönüştürmeleri gerçekleştirir:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Verileri Azure SQL veritabanına taşırken, SQL türünden .NET türüne ve tam tersi de aşağıdaki eşlemeler kullanılır. Eşleme, ADO.NET için SQL Server veri türü eşlemesi ile aynıdır.

| SQL Server veritabanı altyapısı türü | .NET Framework türü |
| --- | --- |
| bigint |Int64 |
| ý |Byte [] |
| sürümleri |Boole |
| Char |Dize, Char [] |
| date |DateTime |
| Hem |DateTime |
| datetime2 |DateTime |
| Türünde |Türünde |
| Kategori |Kategori |
| FıLESTREAM özniteliği (varbinary (max)) |Byte [] |
| Float |Çift |
| image |Byte [] |
| int |Int32 |
| etmenize |Kategori |
| nchar |Dize, Char [] |
| n |Dize, Char [] |
| rakamlardan |Kategori |
| nvarchar |Dize, Char [] |
| gerçek |Tek |
| rowversion |Byte [] |
| girişin |DateTime |
| smallint |Int16 |
| küçük para |Kategori |
| sql_variant |Nesne |
| metin |Dize, Char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Bayt |
| benzersiz tanımlayıcı |Guid |
| ikili |Byte [] |
| varchar |Dize, Char [] |
| xml |'Sini |

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Tekrarlanabilir kopya
Verileri SQL Server veritabanına kopyalarken kopyalama etkinliği varsayılan olarak havuz tablosuna veri ekler. Bunun yerine, daha fazla bilgi almak için bkz. [SqlSink 'e yinelenebilir yazma](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) makalesi.

İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
