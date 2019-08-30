---
title: Veri kopyalama/Azure SQL veri ambarı | Microsoft Docs
description: Azure Data Factory kullanarak Azure SQL veri ambarı/deposundan veri kopyalamayı öğreneceksiniz
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7570cfc8a9804f753a9de140a71436bcc0cebb43
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836644"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL veri ambarı gelen ve giden veri kopyalama
> [!div class="op_single_selector" title1="Data Factory hizmetinin kullandığınız sürümü seçin:"]
> * [Sürüm 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz [V2'de Azure SQL veri ambarı Bağlayıcısı](../connector-azure-sql-data-warehouse.md).

Bu makalede, Azure SQL veri ambarı gönderip buralardan veri taşımak için Azure Data Factory kopyalama etkinliği kullanmayı açıklar. Yapılar [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi, kopyalama etkinliği ile verileri taşıma genel bir bakış sunar.

> [!TIP]
> En iyi performansı elde etmek için Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanın. [Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanma](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) Ayrıntılar bölümünde bulunur. Kullanım örneği ile bir kılavuz için bkz. [1 TB 15 dakikadan daha kısa Azure Data Factory ile Azure SQL Data Warehouse'a veri yükleme](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Desteklenen senaryolar
Veri kopyalayabilirsiniz **Azure SQL veri ambarı'ndan** aşağıdaki verilere depolar:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarından veri kopyalayabilirsiniz **Azure SQL veri ambarı**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Tablo hedef depoda mevcut değilse data SQL Server veya Azure SQL veritabanından Azure SQL veri ambarı'na kopyalama yapılırken, Data Factory otomatik olarak tabloyu SQL veri ambarı'nda kaynak veri deposunda tablonun şeması kullanarak oluşturabilirsiniz. Bkz: [Otomatik Tablo oluşturma](#auto-table-creation) Ayrıntılar için.

## <a name="supported-authentication-type"></a>Desteklenen kimlik doğrulama türü
Azure SQL veri ambarı Bağlayıcısı desteğini temel kimlik doğrulaması.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API'lerini kullanarak bir Azure SQL veri ambarı gönderip buralardan veri taşıyan kopyalama etkinliği ile işlem hattı oluşturabilirsiniz.

Azure SQL veri ambarı/deposundan kopyalayan bir işlem hattı oluşturmak için en kolay yolu, veri kopyalama Sihirbazı'nı kullanmaktır. Bkz: [Öğreticisi: Data Factory ile SQL veri ambarı'na veri yükleme](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) veri kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma hızlı bir kılavuz.

Ayrıca, bir işlem hattı oluşturmak için aşağıdaki araçları kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**, ve **REST API**. Bkz: [kopyalama etkinliği Öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kopyalama etkinliği ile işlem hattı oluşturmak adım adım yönergeler için.

API'ler ve Araçlar kullanmanıza bakılmaksızın, bir havuz veri deposu için bir kaynak veri deposundan veri taşıyan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirin:

1. Oluşturma bir **veri fabrikası**. Veri fabrikası, bir veya daha fazla işlem hattı içerebilir. 
2. Oluşturma **bağlı hizmetler** girdi ve çıktı verilerini bağlamak için veri fabrikanıza depolar. Örneğin, verileri bir Azure blob depolama alanından Azure SQL veri ambarı'na kopyalıyorsanız Azure depolama hesabınızı ve Azure SQL veri ambarı veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. Azure SQL veri ambarı'na özgü bağlı hizmeti özellikleri için bkz: [bağlı hizmeti özellikleri](#linked-service-properties) bölümü. 
3. Oluşturma **veri kümeleri** kopyalama işleminin girdi ve çıktı verilerini göstermek için. Son adımda bahsedilen örnekte, bir veri kümesi blob kapsayıcıyı ve girdi verilerini içeren klasörü belirtin oluşturun. Ayrıca, blob depolama alanından kopyalanan verileri tutan Azure SQL data warehouse'da tablo belirtmek için başka bir veri kümesi oluşturursunuz. Azure SQL veri ambarı'na özgü veri kümesi özellikleri için bkz: [veri kümesi özellikleri](#dataset-properties) bölümü.
4. Oluşturma bir **işlem hattı** bir veri kümesini girdi ve çıktı olarak bir veri kümesini alan kopyalama etkinliği ile. Daha önce bahsedilen örnekte BlobSource bir kaynak ve SqlDWSink havuz olarak kopyalama etkinliği için kullanırsınız. Azure SQL veri ambarı ' Azure Blob depolama alanına kopyalanıyorsa, benzer şekilde, SqlDWSource ve BlobSink kopyalama etkinliği kullanırsınız. Azure SQL veri ambarı'na özel kopyalama etkinliği özellikleri için bkz: [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir kaynak veya havuz bir veri deposunu kullanma hakkında daha fazla ayrıntı için önceki bölümde veri deponuz için bağlantıya tıklayın.

Sihirbazı'nı kullandığınızda, bu Data Factory varlıklarını (bağlı hizmetler, veri kümeleri ve işlem hattı) için JSON tanımları sizin için otomatik olarak oluşturulur. Araçlar/API'leri (dışında .NET API'si) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlayın. Veri gönderip buralardan bir Azure SQL veri ambarı kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları ile örnekleri için bkz [JSON örnekler](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) bu makalenin.

Aşağıdaki bölümler, Azure SQL veri ambarı'na Data Factory varlıklarını belirli tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri
Aşağıdaki tabloda, Azure SQL veri ambarı bağlı hizmete özgü JSON öğeleri için açıklama sağlar.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği ayarlanmalıdır: **AzureSqlDW** |Evet |
| connectionString |ConnectionString özelliği için Azure SQL veri ambarı örneğine bağlanmak için gereken bilgileri belirtin. Temel kimlik doğrulaması desteklenir. |Evet |

> [!IMPORTANT]
> Yapılandırma [Azure SQL veritabanı Güvenlik Duvarı](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) ve veritabanı sunucusuna [Azure hizmetlerinin sunucuya erişmesine izin](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Dış Azure data factory ağ geçidi ile şirket içi veri kaynaklarından dahil olmak üzere Azure SQL veri ambarı'na veri kopyalıyorsanız, ayrıca, Azure SQL veri ambarı'na veri gönderiyor makine için uygun IP adresi aralığı yapılandırın.

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Bölümleri ve veri kümeleri tanımlamak için kullanılabilir özellikleri tam listesi için bkz [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bölümler bir veri kümesi JSON İlkesi yapısı ve kullanılabilirlik gibi tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablo, vs.) için benzer.

TypeProperties bölümünün her tür veri kümesi için farklıdır ve verilerin veri deposundaki konumu hakkında bilgi sağlar. **TypeProperties** türü için veri kümesi bölümünü **AzureSqlDWTable** aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Tablo veya Görünüm başvuran bağlı hizmetin Azure SQL veri ambarı veritabanı adı. |Evet |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Bölümleri & etkinlikleri tanımlamak için mevcut özelliklerin tam listesi için bkz: [işlem hatları oluşturma](data-factory-create-pipelines.md) makalesi. İlke adı ve açıklaması, girdi ve çıktı tabloları gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği, tek bir girdi alır ve tek bir çıktı üretir.

Oysa etkinliğin typeProperties bölümündeki özellikler her etkinlik türü ile farklılık gösterir. Kopyalama etkinliği için kaynaklar ve havuzlar türlerine bağlı olarak farklılık gösterir.

### <a name="sqldwsource"></a>SqlDWSource
Kaynak türü olduğunda **SqlDWSource**, aşağıdaki özellikler kullanılabilir **typeProperties** bölümü:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorgu kullanın. |SQL sorgu dizesi. Örneğin: seçin * MyTable öğesinden. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablo verilerini okuyan saklı yordamın adı. |Saklı yordamın adı. Son SQL deyim bir SELECT deyimi saklı yordam içinde olmalıdır. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Adları ve parametreleri büyük küçük harfleri, adları ve saklı yordam parametreleri büyük küçük harfleri eşleşmelidir. |Hayır |

Varsa **sqlReaderQuery** belirtilen SqlDWSource için kopyalama etkinliği, verileri almak için Azure SQL veri ambarı kaynağına bu sorgu çalıştırır.

Alternatif olarak, bir saklı yordam belirterek belirtebileceğiniz **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordamın parametreleri sürerse).

SqlReaderQuery ya da sqlReaderStoredProcedureName belirtmezseniz JSON veri kümesi yapısı bölümünde tanımlanan sütunları Azure SQL veri ambarına karşı çalıştırmak için bir sorgu oluşturmak için kullanılır. Örnek: `select column1, column2 from mytable`. Veri kümesi tanımı yapısına sahip değilse, tüm sütunları tablodan seçilir.

#### <a name="sqldwsource-example"></a>SqlDWSource örneği

```JSON
"source": {
    "type": "SqlDWSource",
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Belirli bir dilimin veri Temizlenen şekilde yürütmek kopyalama etkinliği için bir sorgu belirtin. Ayrıntılar için bkz [yinelenebilirliği bölümü](#repeatability-during-copy). |Bir sorgu deyimi. |Hayır |
| allowPolyBase |PolyBase, (uygunsa) yerine BULKINSERT mekanizması kullanılıp kullanılmayacağını belirtir. <br/><br/> **PolyBase kullanarak SQL Data Warehouse'a veri yükleme için önerilen yoldur.** Bkz: [Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanma](#use-polybase-to-load-data-into-azure-sql-data-warehouse) kısıtlamaları ve ayrıntıları bölümü. |Doğru <br/>False (varsayılan) |Hayır |
| polyBaseSettings |Bir grup olabilir özellik belirtilen **Bulunan'allowpolybase** özelliği **true**. |&nbsp; |Hayır |
| rejectValue |Sayı veya sorgu başarısız olmadan önce reddedilemiyor satırları yüzdesini belirtir. <br/><br/>PolyBase'nın içinde reddetme seçeneklerini hakkında daha fazla bilgi **bağımsız değişkenleri** bölümünü [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) konu. |0 (varsayılan), 1, 2... |Hayır |
| rejectType |RejectValue seçeneği değişmez değer veya bir yüzdesi olarak belirtilen belirtir. |Değer (varsayılan), yüzde |Hayır |
| rejectSampleValue |Reddedilen satırların yüzdesi PolyBase yeniden hesaplar önce almak için satır sayısını belirler. |1, 2, … |Evet, varsa **rejectType** olduğu **yüzdesi** |
| useTypeDefault |PolyBase metin dosyasından veri aldığında sınırlandırılmış metin dosyaları eksik değerleri nasıl ele alınacağını belirtir.<br/><br/>Bağımsız değişkenler bölümünden bu özellik hakkında daha fazla bilgi [oluşturma EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |TRUE, False (varsayılan) |Hayır |
| writeBatchSize |Arabellek boyutu writeBatchSize ulaştığında veri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| writeBatchTimeout |Toplu ekleme işlemi zaman aşımına uğramadan önce tamamlanması için bir süre bekleyin. |TimeSpan<br/><br/> Örnek: "00: 30:00" (30 dakika). |Hayır |

#### <a name="sqldwsink-example"></a>SqlDWSink örneği

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanma
Kullanarak **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** büyük miktarda veri, yüksek aktarım hızı ile Azure SQL Data Warehouse'a veri yükleme etkili bir yoludur. Yerine varsayılan BULKINSERT mekanizmasını PolyBase kullanarak büyük bir kazanç aktarım hızının görebilirsiniz. Bkz: [kopyalama performans başvuru numarası](data-factory-copy-activity-performance.md#performance-reference) ile ayrıntılı bir karşılaştırması. Kullanım örneği ile bir kılavuz için bkz. [1 TB 15 dakikadan daha kısa Azure Data Factory ile Azure SQL Data Warehouse'a veri yükleme](data-factory-load-sql-data-warehouse.md).

* Veri kaynağınızı ise **Azure Blob veya Azure Data Lake Store**ve PolyBase ile uyumlu bir biçimi, Azure SQL PolyBase kullanarak veri ambarı için doğrudan kopyalayabilirsiniz. Bkz: **[PolyBase kullanarak doğrudan kopyalama](#direct-copy-using-polybase)** ayrıntılarla.
* Kaynak veri deposu ve biçim başlangıçta desteklenmiyor, PolyBase ile kullanabileceğiniz **[PolyBase kullanarak kopyalama aşamalı](#staged-copy-using-polybase)** bunun yerine özellik. Ayrıca, daha iyi aktarım hızı tarafından otomatik olarak verileri PolyBase ile uyumlu biçimine dönüştürmek için kullanılan ve Azure Blob Depolama alanında veri depolama sağlar. Ardından verileri SQL veri ambarı'na yükler.

Ayarlama `allowPolyBase` özelliğini **true** Azure Data Factory, PolyBase, Azure SQL veri ambarı'na veri kopyalamak için kullanmak için aşağıdaki örnekte gösterildiği gibi. Bulunan'allowpolybase true olarak ayarlandığında, PolyBase kullanarak belirli özelliklerini belirtebilirsiniz `polyBaseSettings` özellik grubu. bkz: [SqlDWSink](#sqldwsink) polyBaseSettings ile kullanabileceğiniz özellikleri hakkında ayrıntılar için bölüm.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>PolyBase kullanarak doğrudan kopyalama
SQL veri ambarı PolyBase doğrudan destekleyen Azure Blob ve Azure Data Lake Store (hizmet sorumlusu kullanarak) kaynak olarak ve belirli dosya biçimi gereksinimlerine sahip. Veri kaynağınızı Bu bölümde açıklanan ölçütlere uyuyorsa, kaynak veri deposundan Azure SQL PolyBase kullanarak veri ambarı için doğrudan kopyalayabilirsiniz. Aksi takdirde, kullanabileceğiniz [PolyBase kullanarak kopyalama aşamalı](#staged-copy-using-polybase).

> [!TIP]
> SQL veri ambarı'na Data Lake Store ' verileri verimli bir şekilde kopyalamak için daha fazla bilgi [Azure Data Factory sağlar, bu, hatta daha kolay ve verileri SQL veri ambarı ile Data Lake Store kullanırken açığa çıkarmak kullanışlı](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Gereksinimler karşılanmazsa, Azure Data Factory ayarları denetler ve veri taşıma BULKINSERT mekanizması için otomatik olarak geri döner.

1. **Kaynak bağlı hizmet** türü: **AzureStorage** veya **hizmet sorumlusu kimlik doğrulaması ile birlikte AzureDataLakeStore**.
2. **Girdi veri kümesi** türü: **AzureBlob** veya **birlikte AzureDataLakeStore**ve altında türü biçimi `type` özellikleri **OrcFormat**, **ParquetFormat**, veya **TextFormat** aşağıdaki yapılandırmalarla:

   1. `rowDelimiter` olmalıdır **\n**.
   2. `nullValue` ayarlanır **boş dize** (""), veya `treatEmptyAsNull` ayarlanır **true**.
   3. `encodingName` ayarlanır **utf-8**, olduğu **varsayılan** değeri.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, ve `skipLineCount` belirtilmedi.
   5. `compression` olabilir **sıkıştırma**, **GZip**, veya **Deflate**.

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. Var olan hiçbir `skipHeaderLineCount` altında ayarlama **BlobSource** veya **birlikte AzureDataLakeStore** işlem hattındaki kopyalama etkinliği için.
4. Var olan hiçbir `sliceIdentifierColumnName` bölümündeki **SqlDWSink** işlem hattındaki kopyalama etkinliği için. (Tüm veriler güncelleştirildiğinde veya hiçbir şey tek bir çalıştırmada güncelleştirildiğinde PolyBase garanti eder. Elde etmek için **yinelenebilirliği**, kullanabileceğinizi `sqlWriterCleanupScript`).
5. Var olan hiçbir `columnMapping` ilişkili kopyalama kullanılan etkinlik.

### <a name="staged-copy-using-polybase"></a>PolyBase kullanarak hazırlanmış kopya
Veri kaynağınızı önceki bölümde sunulan ölçütlere uymuyor, geçici hazırlama Azure Blob (Premium depolama olamaz) depolama aracılığıyla veri kopyalamayı etkinleştirebilirsiniz. Bu durumda, Azure Data Factory otomatik olarak dönüştürmeler verileri PolyBase veri biçim gereksinimlerini ve ardından son temizleme ve SQL veri ambarı'na veri yüklemek için PolyBase kullanın, geçici veriler Blob depolama alanından gerçekleştirir. Bkz: [hazırlanmış kopya](data-factory-copy-activity-performance.md#staged-copy) nasıl aracılığıyla hazırlama bir Azure Blob veri kopyalama genel birlikte çalıştığı hakkında bilgi.

> [!NOTE]
> Ne zaman üzerinde bir şirket içi verileri kopyalama verileri Azure SQL Data PolyBase kullanarak Warehouse'a veri depolamak ve hazırlama, veri yönetimi ağ geçidi sürümü 2.4 ise, kaynağınızı dönüştürmek için kullanılan, ağ geçidi makinesinde JRE (Java Çalışma zamanı ortamı) gereklidir doğru biçim verileri. Bu bağımlılık önlemek için en son ağ geçidinize yükseltmeniz önerilir.
>

Bu özelliği kullanmak için oluşturun bir [Azure depolama bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-linked-service) geçici blob depolama alanına sahip Azure depolama hesabına gösterir, ardından belirtin `enableStaging` ve `stagingSettings` gösterildiği gibi kopyalama etkinliği için özellikleri Aşağıdaki kodu:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>PolyBase kullanırken en iyi yöntemler
Aşağıdaki bölümlerde belirtilmiştir olanlara ilave en iyi uygulamalar [en iyi uygulamalar için Azure SQL veri ambarı](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Gerekli database izni
PolyBase kullanmak için SQL Data Warehouse'a veri yükleme için kullanılan kullanıcı gerektiriyor ["CONTROL" izni](https://msdn.microsoft.com/library/ms191291.aspx) hedef veritabanında. Kullanıcı "db_owner" rolünün bir üyesi eklemek için elde etmenin bir yolu var. Bunu izleyerek öğrenin [Bu bölümde](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Satır boyutu ve verileri sınırlama yazın
Polybase yükleri sınırlı yükleme satırları hem küçük **1 MB** ve VARCHR(MAX), NVARCHAR(MAX) veya VARBINARY(MAX) yüklenemiyor. Başvurmak [burada](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Kaynak satır boyutu 1 MB'den büyük verilerle varsa, kaynak tabloları burada her biri en büyük satır boyutu sınırı aşmadığından birkaç küçük parçalara dikey olarak Böl isteyebilirsiniz. Daha küçük tablolar daha sonra PolyBase kullanarak yüklenebilir ve birlikte Azure SQL veri ambarı'nda birleştirildi.

### <a name="sql-data-warehouse-resource-class"></a>SQL veri ambarı kaynak sınıfı
Mümkün olan en iyi performans sağlamak için PolyBase aracılığıyla SQL veri ambarı'na veri yüklemek için kullanılan kullanıcı daha büyük kaynak sınıfı atamak için göz önünde bulundurun. Bunu izleyerek öğrenin [kullanıcı kaynak sınıfı örneğini değiştirmek](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı'nda tableName
Aşağıdaki tabloda belirleme konusunda örnekler **tableName** veri kümesi JSON çeşitli birleşimlerini şema ve tablo adı için bir özellik.

| DB şema | Tablo adı | tableName JSON özelliği |
| --- | --- | --- |
| dbo |MyTable |MyTable ya da dbo. MyTable veya [dbo].[MyTable] |
| dbo1 |MyTable |dbo1. MyTable veya [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] veya [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Aşağıdaki hatayı görürseniz, tableName özelliği için belirtilen değer ile ilgili bir sorun olabilir. TableName JSON özellik değerlerini belirtmek doğru şekilde tabloya bakın.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Varsayılan değeri olan sütunları
Şu anda, Data Factory, PolyBase özelliği yalnızca aynı sayıda sütun hedef tabloda olduğu gibi kabul eder. Söyleyin, dört sütunlarını içeren bir tablo varsa ve bunlardan birini varsayılan değer ile tanımlanır. Giriş verilerini hala dört sütun içermelidir. 3 Sütunlu giriş veri kümesi sağlayan aşağıdaki iletiye benzer bir hata verir:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL değeri, varsayılan değer özel bir biçimidir. Sütun null ise girdi verilerini (blob) söz konusu sütun için boş olabilir (giriş veri kümesinden eksik olamaz). PolyBase, Azure SQL veri ambarı'nda bunlar için NULL ekler.

## <a name="auto-table-creation"></a>Otomatik Tablo oluşturma
SQL Server veya Azure SQL veritabanından Azure SQL veri ambarı'na veri kopyalamak için kopyalama Sihirbazı'nı kullanıyorsanız ve kaynak tabloya karşılık gelen tablosu hedef depoda yok, Data Factory otomatik olarak tablo veri ambarı'nda u tarafından oluşturabilirsiniz Kaynak tablo şemasını güvenebilirler.

Data Factory, kaynak veri deposundaki aynı tablo adı ile hedef depolama tablosu oluşturur. Sütunların veri türleri, aşağıdaki tür eşlemesine göre seçilir. Gerekli olursa, kaynak ve hedef depoları arasında uyumsuzlukları düzeltmek için tür dönüştürmeleri gerçekleştirir. Ayrıca, hepsini bir kez deneme Tablo dağıtımı kullanır.

| Kaynak SQL veritabanı sütun türü | Hedef SQL DW sütun türü (boyut sınırlaması) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| Smallint | Smallint |
| Mini tamsayı | Mini tamsayı |
| bit | bit |
| Decimal | Decimal |
| Numeric | Decimal |
| Float | Float |
| money | money |
| real | real |
| Küçük para | Küçük para |
| binary | binary |
| Varbinary | Varbinary (en fazla 8000) |
| Date | Date |
| Datetime | Datetime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (en fazla 8000) |
| NText | NVarChar (en fazla 4000) |
| Image | VarBinary (en fazla 8000) |
| Benzersiz tanımlayıcı | Benzersiz tanımlayıcı |
| char | char |
| nChar | nChar |
| VarChar | VarChar (en fazla 8000) |
| NVarChar | NVarChar (en fazla 4000) |
| Xml | Varchar (en fazla 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL veri ambarı için tür eşlemesi
Belirtildiği gibi [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesi, kopyalama etkinliği, aşağıdaki 2 adımlı yaklaşım türleriyle havuz için kaynak türünden otomatik tür dönüştürmeleri gerçekleştirir:

1. Yerel kaynak türlerinden .NET türüne dönüştürün
2. .NET türünden yerel havuz türüne dönüştürün

Veri depolamadan Azure SQL veri ambarı taşırken, aşağıdaki eşlemeler SQL türü .NET türü ve bunun tersi de kullanılır.

Eşleme aynı bir [ADO.NET için SQL Server veri türü eşlemesi](https://msdn.microsoft.com/library/cc716729.aspx).

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

Ayrıca, kaynak veri kümesi sütunları havuz veri kümesi kopyalama etkinliği tanımındaki sütunlarından yerine eşleyebilirsiniz. Ayrıntılar için bkz [Azure Data factory'de veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON örnekler ve SQL veri ambarı veri kopyalamak için
Aşağıdaki örnekler kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımları sağlamak [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Bunlar, Azure SQL veri ambarı ve Azure Blob Depolama ve veri kopyalamak nasıl gösterir. Ancak, veriler kopyalanabilir **doğrudan** herhangi birinden herhangi birine belirtilen havuzlarını kaynakları [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopyalama etkinliğini kullanarak Azure Data Factory'de.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Örnek: Verileri Azure SQL veri ambarı ' Azure Blob kopyalama
Örnek, aşağıdaki Data Factory varlıkları tanımlar:

1. Bağlı hizmet türü [AzureSqlDW](#linked-service-properties).
2. Bağlı hizmet türü [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [AzureSqlDWTable](#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) türü [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [işlem hattı](data-factory-create-pipelines.md) kullanan bir kopyalama etkinliği ile [SqlDWSource](#copy-activity-properties) ve [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Örnek zaman serisi (saatlik, günlük, vb.) verileri Azure SQL veri ambarı veritabanındaki bir tabloda bir bloba saatte kopyalar. Bu örneklerde kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

**Azure SQL veri ambarı bağlı hizmeti:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
**Azure SQL veri ambarı veri kümesi girişi:**

Örnek, "MyTable" Azure SQL Data Warehouse'da bir tablo oluşturdunuz ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiği varsayılır.

"Dış" ayarını: "true" bildirir Data Factory hizmetinin veri kümesi dış veri fabrikasına ve veri fabrikasında bir etkinliği tarafından üretilen değil.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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

Veriler her saat yeni bir bloba yazılır (Sıklık: saat, interval: 1). Blob için klasör yolu işlenmekte olan dilimin başlangıç zamanı temel alınarak dinamik olarak değerlendirilir. Yıl, ay, gün ve saat bölümlerini başlangıç zamanı klasör yolu kullanır.

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

**SqlDWSource ve BlobSink ile bir işlem hattındaki kopyalama etkinliği:**

İşlem hattının giriş ve çıkış veri kümelerini kullanmak için yapılandırıldığı ve saatte bir çalışacak şekilde zamanlanmış bir kopyalama etkinliği içeriyor. JSON tanımı, işlem hattındaki **kaynak** türü ayarlandığında **SqlDWSource** ve **havuz** türü ayarlandığında **BlobSink**. SQL sorgusu için belirtilen **SqlReaderQuery** özelliği veri kopyalamak için son bir saat içinde seçer.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> [!NOTE]
> Örnekte, **sqlReaderQuery** SqlDWSource için belirtilir. Kopyalama etkinliği, verileri almak için Azure SQL veri ambarı kaynağına karşı bu sorguyu çalıştırır.
>
> Alternatif olarak, bir saklı yordam belirterek belirtebileceğiniz **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordamın parametreleri sürerse).
>
> SqlReaderQuery ya da sqlReaderStoredProcedureName belirtmezseniz JSON veri kümesi yapısı bölümünde tanımlanan sütunları Azure SQL veri ambarına karşı çalıştırmak için bir sorgu (select Sütun1, Sütun2 mytable gelen) oluşturmak için kullanılır. Veri kümesi tanımı yapısına sahip değilse, tüm sütunları tablodan seçilir.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Örnek: Verileri Azure Blobundan Azure SQL veri ambarı'na kopyalayın.
Örnek, aşağıdaki Data Factory varlıkları tanımlar:

1. Bağlı hizmet türü [AzureSqlDW](#linked-service-properties).
2. Bağlı hizmet türü [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Girdi [veri kümesi](data-factory-create-datasets.md) türü [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Bir çıkış [veri kümesi](data-factory-create-datasets.md) türü [AzureSqlDWTable](#dataset-properties).
5. A [işlem hattı](data-factory-create-pipelines.md) kullanan kopyalama etkinlikli [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) ve [SqlDWSink](#copy-activity-properties).

Zaman serisi, Azure SQL veri ambarı tablosuna verileri (saatlik, günlük, vb.) Azure blob örnek kopya saatte veritabanı. Bu örneklerde kullanılan JSON özellikleri örnekleri aşağıdaki bölümlerde açıklanmıştır.

**Azure SQL veri ambarı bağlı hizmeti:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
**Azure Blob girdi veri kümesi:**

Veri alındığından yeni blobundan her saat (Sıklık: saat, interval: 1). Blob klasörü yolu ve dosya adı dinamik olarak değerlendirilir işlenmekte olan dilimin başlangıç zamanı temel alınarak. Klasör yolu yıl, ay ve gün kısmını başlangıç saati ve dosya adı başlangıç zamanı saat bölümünü kullanır. "dış": "true" ayarı, bu tablo harici veri fabrikasına ve veri fabrikasında bir etkinliği tarafından üretilen değil Data Factory hizmetinin bildirir.

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
**Azure SQL veri ambarı veri kümesi çıktı:**

Örnek verileri Azure SQL veri ambarı'nda "MyTable" adlı bir tabloya kopyalar. Blob CSV dosyasını içerecek şekilde beklediğiniz gibi aynı sayıda sütun ile Azure SQL veri ambarı tablosu oluşturun. Yeni satırlar saatte tablosuna eklenir.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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
**BlobSource ve SqlDWSink ile bir işlem hattındaki kopyalama etkinliği:**

İşlem hattının giriş ve çıkış veri kümelerini kullanmak için yapılandırıldığı ve saatte bir çalışacak şekilde zamanlanmış bir kopyalama etkinliği içeriyor. JSON tanımı, işlem hattındaki **kaynak** türü ayarlandığında **BlobSource** ve **havuz** türü ayarlandığında **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
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
Bir kılavuz için bkz [1 TB 15 dakikadan daha kısa Azure Data Factory ile Azure SQL Data Warehouse'a veri yükleme](data-factory-load-sql-data-warehouse.md) ve [Azure Data Factory ile veri yükleme](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) makale Azure SQL veri ambarı belgelerinde.

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Bkz: [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md) veri taşıma (kopyalama etkinliği) Azure Data Factory ve bunu en iyi duruma getirmek için çeşitli yollar, performansı etkileyebilir anahtar Etkenler hakkında bilgi edinmek için.
