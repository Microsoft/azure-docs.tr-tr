---
title: Azure SQL veri ambarı 'ndan/içinden veri kopyalama
description: Azure Data Factory kullanarak Azure SQL veri ambarı 'na/verilerine veri kopyalamayı öğrenin
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
ms.openlocfilehash: d0306d891b327422383120ef322ece407829f7ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683053"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL veri ambarı 'na veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Azure SQL veri ambarı Bağlayıcısı](../connector-azure-sql-data-warehouse.md).

Bu makalede, Azure SQL veri ambarı 'na/kaynağından veri taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

> [!TIP]
> En iyi performansı elde etmek için PolyBase 'i kullanarak Azure SQL veri ambarı 'na veri yükleyin. [Verileri Azure SQL veri ambarı 'na yüklemek Için PolyBase kullanma](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) bölümünde ayrıntılar bulunur. Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında 1 TB 'Yi Azure SQL veri ambarı 'Na yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
**Azure SQL veri ambarı 'ndan** aşağıdaki veri depolarına veri kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarından verileri **Azure SQL veri ambarı 'na**kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> SQL Server veya Azure SQL veritabanından Azure SQL veri ambarı 'na veri kopyalarken, tablo hedef depoda yoksa, Data Factory tabloyu kaynak veri deposundaki tablonun şemasını kullanarak SQL Data Warehouse 'da otomatik olarak oluşturabilir. Ayrıntılar için bkz. [Otomatik tablo oluşturma](#auto-table-creation) .

## <a name="supported-authentication-type"></a>Desteklenen kimlik doğrulama türü
Azure SQL veri ambarı Bağlayıcısı, temel kimlik doğrulamasını destekler.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak bir Azure SQL veri ambarına veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

Azure SQL veri ambarı 'na/verilerine veri kopyalayan bir işlem hattı oluşturmanın en kolay yolu, veri kopyalama Sihirbazı 'nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak bir işlem hattı oluşturma hakkında hızlı bir yol için [Data Factory Ile SQL veri ambarı 'na veri yükleme](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) makalesine bakın.

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Bir **Veri Fabrikası**oluşturun. Bir veri fabrikası bir veya daha fazla işlem hattı içerebilir. 
2. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun. Örneğin, bir Azure Blob depolama alanından Azure SQL veri ambarı 'na veri kopyalıyorsanız, Azure depolama hesabınızı ve Azure SQL veri Ambarınızı veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. Azure SQL veri ambarı 'na özgü bağlantılı hizmet özellikleri için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü. 
3. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. Son adımda bahsedilen örnekte, blob kapsayıcısını ve girdi verilerini içeren klasörü belirtmek için bir veri kümesi oluşturursunuz. Ve, blob depolamadan kopyalanmış verileri tutan Azure SQL veri ambarı 'nda tabloyu belirtmek için başka bir veri kümesi oluşturursunuz. Azure SQL veri ambarı 'na özgü veri kümesi özellikleri için bkz. [veri kümesi özellikleri](#dataset-properties) bölümü.
4. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. Daha önce bahsedilen örnekte, BlobSource değerini kaynak ve SqlDWSink olarak kopyalama etkinliği için havuz olarak kullanırsınız. Benzer şekilde, Azure SQL veri ambarı 'ndan Azure Blob depolama alanına kopyalama yapıyorsanız kopyalama etkinliğinde SqlDWSource ve BlobSink kullanın. Azure SQL veri ambarı 'na özgü kopyalama etkinliği özellikleri için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir veri deposunu kaynak veya havuz olarak kullanma hakkında ayrıntılı bilgi için, veri deponuzdaki önceki bölümde yer alan bağlantıya tıklayın.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Bir Azure SQL veri ambarına veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları içeren örnekler için, bu makalenin [JSON örnekleri](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) bölümüne bakın.

Aşağıdaki bölümler, Azure SQL veri ambarı 'na özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, Azure SQL veri ambarı bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **Azuresqldw** olarak ayarlanmalıdır |Evet |
| connectionString |ConnectionString özelliği için Azure SQL veri ambarı örneğine bağlanmak için gereken bilgileri belirtin. Yalnızca temel kimlik doğrulaması desteklenir. |Evet |

> [!IMPORTANT]
> Azure [hizmetlerinin sunucuya erişmesine izin](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)vermek IÇIN [Azure SQL veritabanı güvenlik duvarını](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) ve veritabanı sunucusunu yapılandırın. Ayrıca, Data Factory ağ geçidine sahip şirket içi veri kaynakları dahil Azure dışından Azure SQL veri ambarı 'na veri kopyalıyorsanız, Azure SQL veri ambarı 'na veri gönderen makine için uygun IP adresi aralığını yapılandırın.

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Azuresqldwtable** türündeki veri kümesinin **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu Azure SQL veri ambarı veritabanında tablonun veya görünümün adı. |Evet |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği yalnızca bir girdi alır ve yalnızca bir çıktı üretir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

### <a name="sqldwsource"></a>SqlDWSource
Kaynak **Sqldwsource**türünde olduğunda, **typeproperties** bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: select * from MyTable. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablodaki verileri okuyan saklı yordamın adı. |Saklı yordamın adı. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |

SqlDWSource için **Sqlreaderquery** belirtilmişse, kopyalama etkinliği verileri almak için bu SORGUYU Azure SQL veri ambarı kaynağında çalıştırır.

Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, Azure SQL veri ambarı 'nda çalıştırılacak bir sorgu oluşturmak için JSON veri kümesinin yapı bölümünde tanımlanan sütunlar kullanılır. Örnek: `select column1, column2 from mytable`. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

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
**Sqldwsink** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Bir kopyalama etkinliğinin yürütülmesi için belirli bir dilim verilerinin temizlenmesi için bir sorgu belirtin. Ayrıntılar için bkz. [yinelenebilirlik bölümü](#repeatability-during-copy). |Sorgu ekstresi. |Hayır |
| allowPolyBase |BULKıNSERT mekanizması yerine PolyBase 'in (uygun olduğunda) kullanılıp kullanılmayacağını belirtir. <br/><br/> **PolyBase 'in kullanılması, verileri SQL veri ambarı 'na yüklemek için önerilen yoldur.** Kısıtlamalar ve Ayrıntılar için bkz. [PolyBase 'ı kullanarak Azure SQL veri ambarı 'na veri yükleme](#use-polybase-to-load-data-into-azure-sql-data-warehouse) bölümüne bakın. |True <br/>False (varsayılan) |Hayır |
| polyBaseSettings |**Allowpolybase** özelliği **true**olarak ayarlandığında belirtilenebilir bir özellik grubu. |&nbsp; |Hayır |
| rejectValue |Sorgu başarısız olmadan önce reddedilecek satırların sayısını veya yüzdesini belirtir. <br/><br/>[Dış tablo oluşturma (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) konusunun **bağımsız değişkenler** bölümünde PolyBase 'in reddetme seçenekleri hakkında daha fazla bilgi edinin. |0 (varsayılan), 1, 2,... |Hayır |
| rejectType |RejectValue seçeneğinin sabit değer değeri mi yoksa yüzde olarak mı belirtilmediğini belirtir. |Değer (varsayılan), yüzde |Hayır |
| rejectSampleValue |PolyBase reddedilen satırların yüzdesini yeniden hesaplamadan önce alınacak satır sayısını belirler. |1, 2,... |Evet, **rejectType** **ise** |
| useTypeDefault |PolyBase metin dosyasından verileri aldığında, sınırlandırılmış metin dosyalarında eksik değerlerin nasıl işleneceğini belirtir.<br/><br/>[Dış dosya biçimi oluşturma (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)Içindeki bağımsız değişkenler bölümünden bu özellik hakkında daha fazla bilgi edinin. |True, false (varsayılan) |Hayır |
| writeBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |

#### <a name="sqldwsink-example"></a>SqlDWSink örneği

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'na veri yüklemek için PolyBase kullanma
**[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** 'in kullanılması, yüksek aktarım hızı Ile Azure SQL veri ambarı 'na büyük miktarda veri yüklemenin etkili bir yoludur. Varsayılan BULKıNSERT mekanizması yerine PolyBase kullanarak üretilen iş için büyük bir kazanç görebilirsiniz. Bkz. ayrıntılı karşılaştırma ile [kopyalama performansı başvuru numarası](data-factory-copy-activity-performance.md#performance-reference) . Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında 1 TB 'Yi Azure SQL veri ambarı 'Na yükleme](data-factory-load-sql-data-warehouse.md)bölümüne bakın.

* Kaynak verileriniz **Azure Blobu veya Azure Data Lake Store**ise ve biçim PolyBase ile uyumluysa, PolyBase kullanarak doğrudan Azure SQL veri ambarı 'na kopyalayabilirsiniz. Bkz. **[PolyBase 'i kullanarak doğrudan kopyalama](#direct-copy-using-polybase)** ayrıntıları.
* Kaynak veri deponuzu ve formatı İlk olarak PolyBase tarafından desteklenmiyorsa, bunun yerine **[PolyBase özelliğini kullanarak hazırlanan kopyayı](#staged-copy-using-polybase)** kullanabilirsiniz. Ayrıca, verileri otomatik olarak PolyBase uyumlu biçime dönüştürerek ve verileri Azure Blob depolamada depolayarak daha iyi bir aktarım hızı sağlar. Daha sonra verileri SQL veri ambarı 'na yükler.

Azure SQL veri ambarı 'na veri kopyalamak için PolyBase 'i kullanmak üzere Azure Data Factory için aşağıdaki örnekte gösterildiği gibi `allowPolyBase` özelliğini **true** olarak ayarlayın. AllowPolyBase 'i true olarak belirlediğinizde, `polyBaseSettings` Özellik grubunu kullanarak PolyBase 'e özgü özellikleri belirtebilirsiniz. polyBaseSettings ile kullanabileceğiniz özellikler hakkında ayrıntılı bilgi edinmek için [Sqldwsink](#sqldwsink) bölümüne bakın.

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
SQL veri ambarı PolyBase, kaynak olarak ve belirli dosya biçimi gereksinimleriyle Azure Blob ve Azure Data Lake Store (hizmet sorumlusu kullanarak) doğrudan destek. Kaynak verileriniz bu bölümde açıklanan ölçütleri karşılıyorsa, PolyBase kullanarak kaynak veri deposundan doğrudan Azure SQL veri ambarı 'na kopyalayabilirsiniz. Aksi halde, [PolyBase kullanarak hazırlanmış kopyayı](#staged-copy-using-polybase)kullanabilirsiniz.

> [!TIP]
> Data Lake Store verileri SQL veri ambarı 'na verimli bir şekilde kopyalamak için, Azure Data Factory daha fazla bilgi edinin, [SQL veri ambarı ile Data Lake Store kullanırken verilerin öngörülerini daha kolay ve](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)kolay bir şekilde açığa çıkarabilir.

Gereksinimler karşılanmazsa, Azure Data Factory ayarları denetler ve veri taşıma için otomatik olarak BULKıNSERT mekanizmasına geri döner.

1. **Kaynak bağlı hizmet** , **hizmet sorumlusu kimlik doğrulamasıyla**: **azurestorage** veya AzureDataLakeStore türünde.
2. **Giriş veri kümesi** şunlardır: **AzureBlob** veya **AzureDataLakeStore**ve `type` Properties altındaki Biçim türü, aşağıdaki yapılandırmalarda **orcformat**, **parquetformat**veya **TextFormat** ' dir:

   1. `rowDelimiter` **\n**olmalıdır.
   2. `nullValue`, **boş dize** ("") olarak ayarlanır veya `treatEmptyAsNull` **true**olarak ayarlanır.
   3. `encodingName`, **varsayılan** değer olan **UTF-8**olarak ayarlanır.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`ve `skipLineCount` belirtilmemiş.
   5. `compression` **sıkıştırma**, **gzip**veya **söndür**olamaz.

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

3. İşlem hattındaki kopyalama etkinliği için **Blobsource** veya **AzureDataLakeStore** altında `skipHeaderLineCount` ayarı yoktur.
4. İşlem hattındaki kopyalama etkinliği için **Sqldwsink** altında `sliceIdentifierColumnName` ayarı yok. (PolyBase, tüm verilerin güncelleştirildiğini veya tek bir çalıştırmada hiçbir şey güncelleştirilmediğini garanti eder. **Yinelenebilirlik**ulaşmak için `sqlWriterCleanupScript`) kullanabilirsiniz.
5. İlişkili kopyalama etkinliğinde kullanılan `columnMapping` yok.

### <a name="staged-copy-using-polybase"></a>PolyBase kullanarak hazırlanmış kopya
Kaynak verileriniz önceki bölümde tanıtılan ölçütlere uymazsa, verileri geçici hazırlama Azure Blob depolama (Premium Depolama olamaz) aracılığıyla kopyalamayı etkinleştirebilirsiniz. Bu durumda Azure Data Factory, PolyBase 'in veri biçimi gereksinimlerini karşılamak üzere verilerde otomatik olarak dönüştürmeler gerçekleştirir, sonra SQL veri ambarı 'na veri yüklemek için PolyBase 'i kullanır ve BLOB depolama alanındaki geçici verilerinizi en son temizleme. Hazırlama Azure blobu aracılığıyla verilerin nasıl kopyalandığı hakkında ayrıntılı bilgi için bkz. [aşamalı kopya](data-factory-copy-activity-performance.md#staged-copy) , genel olarak çalışma.

> [!NOTE]
> PolyBase ve hazırlama kullanarak şirket içi veri deposundaki verileri Azure SQL veri ambarı 'na kopyalarken, Veri Yönetimi ağ geçidi sürümünüz 2,4 altındaysa, kaynağınızı dönüştürmek için kullanılan ağ geçidi makinenizde JRE (Java Runtime Environment) gerekir veriler doğru biçime sahip. Böyle bir bağımlılığı önlemek için ağ geçidinizi en son sürümüne yükseltmenizi önerin.
>

Bu özelliği kullanmak için, ara BLOB depolama alanına sahip Azure depolama hesabına başvuran bir [Azure depolama bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-linked-service) oluşturun, ardından aşağıdaki kodda gösterildiği gibi kopyalama etkinliğinin `enableStaging` ve `stagingSettings` özelliklerini belirtin:

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

## <a name="best-practices-when-using-polybase"></a>PolyBase kullanırken en iyi uygulamalar
Aşağıdaki bölümlerde, [Azure SQL veri ambarı Için en iyi yöntemler](../../sql-data-warehouse/sql-data-warehouse-best-practices.md)bölümünde bahsedilen diğer en iyi yöntemler sağlanmaktadır.

### <a name="required-database-permission"></a>Gerekli veritabanı izni
PolyBase 'i kullanmak için, SQL veri ambarı 'na veri yüklemek için kullanılan kullanıcının hedef veritabanında ["Control" iznine](https://msdn.microsoft.com/library/ms191291.aspx) sahip olması gerekir. Bunu yapmanın bir yolu, bu kullanıcıyı "db_owner" rolünün bir üyesi olarak eklemektir. [Bu bölümü](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)izleyerek bunu nasıl yapacağınızı öğrenin.

### <a name="row-size-and-data-type-limitation"></a>Satır boyutu ve veri türü sınırlaması
PolyBase yükleri **1 MB** 'tan küçük satırları yüklemek ve VARCHR (max), nvarchar (max) veya VARBINARY (max) öğesine yüklenemez. [Buraya](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)bakın.

Boyutu 1 MB 'tan büyük olan kaynak verileriniz varsa, kaynak tabloları her birinin en büyük satır boyutunun sınırı aşmadığı birkaç küçük yere bölmek isteyebilirsiniz. Daha sonra PolyBase kullanılarak yüklenebilir ve Azure SQL veri ambarı 'nda birlikte birleştirilir.

### <a name="sql-data-warehouse-resource-class"></a>SQL veri ambarı kaynak sınıfı
Mümkün olan en iyi verimi elde etmek için, PolyBase aracılığıyla SQL veri ambarı 'na veri yüklemek için kullanılan kullanıcıya daha büyük kaynak sınıfı atamayı göz önünde bulundurun. Bunun nasıl yapılacağını, [bir Kullanıcı kaynak sınıfı örneğini değiştirme](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)hakkında bilgi edinin.

### <a name="tablename-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda tableName
Aşağıdaki tabloda, şema ve tablo adının çeşitli birleşimleri için veri kümesi JSON içinde **TableName** özelliğinin nasıl belirtilme hakkında örnekler verilmektedir.

| DB şeması | Tablo adı | tableName JSON özelliği |
| --- | --- | --- |
| dbo |MyTable |MyTable veya dbo. MyTable veya [dbo]. MyTable |
| dbo1 |MyTable |dbo1. MyTable veya [dbo1]. MyTable |
| dbo |My. Table |[My. Table] veya [dbo]. [My. Table] |
| dbo1 |My. Table |[dbo1]. [My. Table] |

Aşağıdaki hatayı görürseniz, tableName özelliği için belirttiğiniz değerle ilgili bir sorun olabilir. TableName JSON özelliği için değerleri belirtmenin doğru yolu için tabloya bakın.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Varsayılan değerlere sahip sütunlar
Şu anda Data Factory PolyBase özelliği, hedef tablodaki aynı sayıda sütunu kabul eder. Dört sütunlu bir tablonuz olduğunu ve bunlardan birinin varsayılan bir değerle tanımlandığını varsayalım. Giriş verileri hala dört sütun içermelidir. 3 sütunlu bir giriş veri kümesi sağlamak aşağıdaki iletiye benzer bir hata verir:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL değer, varsayılan değer olan özel bir formdur. Sütun null atanabilir ise, bu sütun için giriş verileri (blob 'ta) boş olabilir (girdi veri kümesinde eksik olamaz). PolyBase, Azure SQL veri ambarı 'nda bunlar için NULL değeri ekler.

## <a name="auto-table-creation"></a>Otomatik tablo oluşturma
SQL Server veya Azure SQL veritabanından Azure SQL veri ambarı 'na veri kopyalamak için kopyalama Sihirbazı 'Nı kullanıyorsanız ve kaynak tabloya karşılık gelen tablo hedef depoda yoksa, Data Factory tabloyu u tarafından veri ambarında otomatik olarak oluşturabilir kaynak tablo şeması atılıyor.

Data Factory, kaynak veri deposunda aynı tablo adına sahip hedef depoda tablo oluşturur. Sütunların veri türleri aşağıdaki tür eşlemesine göre seçilir. Gerekirse, kaynak ve hedef depolarla ilgili uyumsuzlukları onarmak için tür dönüştürmeleri gerçekleştirir. Ayrıca hepsini bir kez deneme tablo dağıtımı kullanır.

| Kaynak SQL veritabanı sütun türü | Hedef SQL DW sütun türü (boyut sınırlaması) |
| --- | --- |
| 'Tir | 'Tir |
| BigInt | BigInt |
| Small | Small |
| Iç | Iç |
| sürümleri | sürümleri |
| Kategori | Kategori |
| rakamlardan | Kategori |
| Float | Float |
| etmenize | etmenize |
| Real | Real |
| Küçük para | Küçük para |
| ý | ý |
| ikili | Varbinary (8000 'e kadar) |
| Tarih | Tarih |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Zaman | Zaman |
| Türünde | Türünde |
| Girişin | Girişin |
| Metin | Varchar (8000 'e kadar) |
| N | NVarChar (4000 'e kadar) |
| Görüntü | VarBinary (8000 'e kadar) |
| Benzersiz tanımlayıcı | Benzersiz tanımlayıcı |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (8000 'e kadar) |
| NVarChar | NVarChar (4000 'e kadar) |
| 'Sini | Varchar (8000 'e kadar) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL veri ambarı için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi kopyalama etkinliği, aşağıdaki 2 adımlı yaklaşımla kaynak türlerindeki otomatik tür dönüştürmeleri, havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Verileri Azure SQL veri ambarı 'ndan & taşırken, SQL türünden .NET türüne ve tam tersi bir şekilde aşağıdaki eşlemeler kullanılır.

Eşleme, [ADO.NET için SQL Server veri türü eşlemesi](https://msdn.microsoft.com/library/cc716729.aspx)ile aynıdır.

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

Ayrıca, kaynak veri kümesindeki sütunları, kopyalama etkinliği tanımındaki havuz veri kümesinden sütunlara eşleyebilirsiniz. Ayrıntılar için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>SQL veri ambarı 'na veri kopyalamak için JSON örnekleri
Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Azure SQL veri ambarı ve Azure Blob depolama 'ya veri kopyalamayı gösterir. Ancak, veriler, Azure Data Factory ' deki kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir kaynaktan herhangi bir kaynağa **doğrudan** kopyalanabilir.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Örnek: Azure SQL veri ambarı 'ndan Azure Blob 'a veri kopyalama
Örnek, aşağıdaki Data Factory varlıklarını tanımlar:

1. [Azuresqldw](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Azuresqldwtable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Sqldwsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, Azure SQL veri ambarı veritabanındaki bir tablodan saat serisi (saatlik, günlük, vb.) verilerini her saat bir bloba kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

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
**Azure SQL veri ambarı giriş veri kümesi:**

Örnek, Azure SQL veri ambarı 'nda bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiğini varsayar.

"External": "true" ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

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

**SqlDWSource ve BlobSink ile işlem hattındaki etkinliği kopyalama:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **sqldwsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sqlreaderquery** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

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
> Örnekte, SqlDWSource için **Sqlreaderquery** belirtilir. Kopyalama etkinliği, verileri almak için Azure SQL veri ambarı kaynağına karşı bu sorguyu çalıştırır.
>
> Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.
>
> SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, JSON veri kümesinin yapı bölümünde tanımlanan sütunlar, Azure SQL veri ambarı 'na karşı çalıştırmak için bir sorgu oluşturmak (myTable 'den Sütun1 'den Sütun2 'yi seçin) için kullanılır. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Örnek: Azure Blobundan Azure SQL veri ambarı 'na veri kopyalama
Örnek, aşağıdaki Data Factory varlıklarını tanımlar:

1. [Azuresqldw](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [Azuresqldwtable](#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) ve [Sqldwsink](#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, zaman serisi verilerini (saatlik, günlük, vb.) Azure blobundan her saat Azure SQL veri ambarı veritabanındaki bir tabloya kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

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
**Azure Blob giriş veri kümesi:**

Veriler her saat yeni bir bloba alınır (sıklık: saat, Aralık: 1). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün bölümünü ve dosya adını kullanır başlangıç zamanının saat kısmını kullanır. "External": "true" ayarı, bu tablonun veri fabrikasında dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

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
**Azure SQL veri ambarı çıkış veri kümesi:**

Örnek, verileri Azure SQL veri ambarı 'nda "MyTable" adlı bir tabloya kopyalar. Blob CSV dosyasının içermesini istediğiniz sayıda sütun içeren Azure SQL veri ambarı 'nda tablo oluşturun. Yeni satırlar tabloya her saat eklenir.

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
**BlobSource ve SqlDWSink ile işlem hattındaki etkinliği kopyalama:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **sqldwsink**olarak ayarlanır.

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
İzlenecek yol için, Azure SQL veri ambarı belgelerindeki Azure Data Factory ve [Azure Data Factory ile verileri yükleme](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) makalesine bkz. [1 TB 'Yi Azure SQL veri ambarı 'nda yükleme](data-factory-load-sql-data-warehouse.md) .

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
