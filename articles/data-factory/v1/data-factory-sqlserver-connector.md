---
title: SQL Server veri taşıma
description: Azure Data Factory kullanarak şirket içinde veya Azure sanal makinesinde bulunan SQL Server veritabanına veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a5afbec39a87423463bf1a65fdd99ec7a739958b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666269"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Azure Data Factory kullanarak şirket içi SQL Server veya IaaS (Azure VM) üzerinde veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-sqlserver-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-sql-server.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de SQL Server Bağlayıcısı](../connector-sql-server.md)' na bakın.

Bu makalede, şirket içi SQL Server veritabanına veri taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Desteklenen senaryolar
**SQL Server veritabanından** aşağıdaki veri depolarına veri kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarından verileri **bir SQL Server veritabanına**kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Desteklenen SQL Server sürümleri
Bu SQL Server Bağlayıcısı, hem SQL kimlik doğrulaması hem de Windows kimlik doğrulaması kullanılarak şirket içinde barındırılan veya Azure IaaS 'de barındırılan aşağıdaki örnek sürümlerinden veri kopyalamayı destekler: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 SQL Server 2005

## <a name="enabling-connectivity"></a>Bağlantı etkinleştiriliyor
Şirket içinde veya Azure IaaS (hizmet olarak altyapı) VM 'lerinde barındırılan SQL Server ile bağlantı için gereken kavramlar ve adımlar aynıdır. Her iki durumda da bağlantı için Veri Yönetimi ağ geçidi kullanmanız gerekir.

Veri Yönetimi ağ geçidini ayarlama hakkında bilgi edinmek ve ağ geçidini ayarlamaya yönelik adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makaleleri arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) . Bir ağ geçidi örneği ayarlamak SQL Server ile bağlantı kurmak için bir ön koşul örneğidir.

Daha iyi performans için SQL Server aynı şirket içi makineye veya bulut VM örneğine ağ geçidi yükleyebilirsiniz, ancak bunları ayrı makinelere yüklemenizi öneririz. Ağ geçidinin ve SQL Server ayrı makinelerde olması, kaynak çekişmesini azaltır.

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak, verileri şirket içi SQL Server veritabanına taşıyan kopyalama etkinliğiyle bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Bir **Veri Fabrikası**oluşturun. Bir veri fabrikası bir veya daha fazla işlem hattı içerebilir.
2. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun. Örneğin, bir SQL Server veritabanından Azure Blob depolama alanına veri kopyalıyorsanız, SQL Server veritabanınızı ve Azure Storage hesabınızı veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. SQL Server veritabanına özgü bağlantılı hizmet özellikleri için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.
3. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. Son adımda bahsedilen örnekte, giriş verilerini içeren SQL Server veritabanınızdaki SQL tablosunu belirtmek için bir veri kümesi oluşturursunuz. Ve, blob kapsayıcısını ve SQL Server veritabanından kopyalanmış verileri tutan klasörü belirtmek için başka bir veri kümesi oluşturursunuz. SQL Server veritabanına özgü veri kümesi özellikleri için bkz. [DataSet özellikleri](#dataset-properties) bölümü.
4. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. Daha önce bahsedilen örnekte, kopyalama etkinliği için kaynak ve BlobSink olarak SqlSource ' u bir havuz olarak kullanacaksınız. Benzer şekilde, Azure Blob depolama 'dan SQL Server veritabanına kopyalama yapıyorsanız, kopyalama etkinliğinde BlobSource ve SqlSink kullanırsınız. SQL Server veritabanına özgü kopyalama etkinliği özellikleri için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir veri deposunu kaynak veya havuz olarak kullanma hakkında ayrıntılı bilgi için, veri deponuzdaki önceki bölümde yer alan bağlantıya tıklayın.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Şirket içi SQL Server veritabanına veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları içeren örnekler için, bu makalenin [JSON örnekleri](#json-examples-for-copying-data-from-and-to-sql-server) bölümüne bakın.

Aşağıdaki bölümler SQL Server özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Şirket içi SQL Server veritabanını bir veri fabrikasına bağlamak için **OnPremisesSqlServer** türünde bağlı bir hizmet oluşturursunuz. Aşağıdaki tabloda, şirket içi SQL Server bağlı hizmetine özgü JSON öğeleri için açıklama sağlanmaktadır.

Aşağıdaki tabloda SQL Server bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **OnPremisesSqlServer**olarak ayarlanmalıdır. |Evet |
| connectionString |SQL kimlik doğrulaması veya Windows kimlik doğrulaması kullanarak şirket içi SQL Server veritabanına bağlanmak için gerekli connectionString bilgilerini belirtin. |Evet |
| gatewayName |Data Factory hizmetinin şirket içi SQL Server veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Evet |
| kullanıcı adı |Windows kimlik doğrulamasını kullanıyorsanız Kullanıcı adını belirtin. Örnek: **domainname\\Kullanıcı adı**. |Hayır |
| password |Kullanıcı adı için belirttiğiniz kullanıcı hesabı için parola belirtin. |Hayır |

**New-AzDataFactoryEncryptValue** cmdlet 'ini kullanarak kimlik bilgilerini şifreleyebilir ve aşağıdaki örnekte gösterildiği gibi bunları bağlantı dizesinde kullanabilirsiniz (**encryptedcredential** özelliği):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Örnekler
**SQL kimlik doğrulaması kullanmak için JSON**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**Windows kimlik doğrulamasını kullanmak için JSON**

Veri Yönetimi ağ geçidi, şirket içi SQL Server veritabanına bağlanmak için belirtilen kullanıcı hesabının kimliğine bürünecektir.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Örneklerde, bir SQL Server veritabanındaki bir tabloyu göstermek için **Sqlservertable** türünde bir veri kümesi kullandınız.

Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (SQL Server, Azure blob, Azure tablosu vb.) için benzerdir.

TypeProperties bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Sqlservertable** türündeki veri kümesinin **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlı hizmetin başvurduğu SQL Server veritabanı örneğindeki tablonun veya görünümün adı. |Evet |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Verileri bir SQL Server veritabanından taşıyorsanız, kopyalama etkinliğindeki kaynak türünü **SQLSource**olarak ayarlarsınız. Benzer şekilde, verileri bir SQL Server veritabanına taşıyorsanız, kopyalama etkinliğindeki havuz türünü **Sqlsink**olarak ayarlarsınız. Bu bölüm, SqlSource ve SqlSink tarafından desteklenen özelliklerin bir listesini sağlar.

Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilkeler gibi özellikler, tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği yalnızca bir girdi alır ve yalnızca bir çıktı üretir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

### <a name="sqlsource"></a>SqlSource
Bir kopyalama etkinliğindeki kaynak **SQLSource**türünde olduğunda, **typeproperties** bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sqlReaderQuery |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: select * from MyTable. Giriş veri kümesi tarafından başvurulan veritabanından birden fazla tabloya başvurabilir. Belirtilmemişse, çalıştırılan SQL deyiminizi seçin: tablom arasından seçim yapın. |Hayır |
| sqlReaderStoredProcedureName |Kaynak tablodaki verileri okuyan saklı yordamın adı. |Saklı yordamın adı. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |

SqlSource için **Sqlreaderquery** belirtilmişse, kopyalama etkinliği verileri almak için bu sorguyu SQL Server veritabanı kaynağına göre çalıştırır.

Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, yapı bölümünde tanımlanan sütunlar SQL Server veritabanında çalıştırılacak bir seçme sorgusu oluşturmak için kullanılır. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

> [!NOTE]
> **SqlReaderStoredProcedureName**kullandığınızda JSON veri kümesindeki **TableName** özelliği için de bir değer belirtmeniz gerekir. Ancak bu tabloda gerçekleştirilen hiçbir doğrulama yoktur.

### <a name="sqlsink"></a>SqlSink
**Sqlsink** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| writeBatchTimeout |Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |TimeSpan<br/><br/> Örnek: "00:30:00" (30 dakika). |Hayır |
| writeBatchSize |Arabellek boyutu writeBatchSize ulaştığında verileri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 10000) |
| sqlWriterCleanupScript |Belirli bir dilim verilerinin temizlenmesi gibi, yürütülecek kopyalama etkinliğinin sorgusunu belirtin. Daha fazla bilgi için bkz. [yinelenebilir kopyalama](#repeatable-copy) bölümü. |Sorgu ekstresi. |Hayır |
| Dilimleyiceıdentifiercolumnname |Yeniden çalıştırıldığında belirli bir dilimin verilerini temizlemek için kullanılan otomatik olarak oluşturulan dilim tanımlayıcısı ile doldurulacak kopyalama etkinliğinin sütun adını belirtin. Daha fazla bilgi için bkz. [yinelenebilir kopyalama](#repeatable-copy) bölümü. |Binary (32) veri türüne sahip bir sütunun sütun adı. |Hayır |
| sqlWriterStoredProcedureName |Hedef tabloya kaynak verilerinin nasıl uygulanacağını tanımlayan saklı yordamın adı (örneğin, kendi iş mantığınızı kullanarak). <br/><br/>Bu saklı yordamın **toplu iş başına çağrılacağını**aklınızda görürsünüz. Yalnızca bir kez çalıştırılan ve kaynak verilerle hiçbir şey olmayan bir işlem yapmak istiyorsanız, `sqlWriterCleanupScript` özelliğini kullanın. |Saklı yordamın adı. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri. |Ad/değer çiftleri. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. |Hayır |
| sqlWriterTableType |Saklı yordamda kullanılacak tablo türü adını belirtin. Kopyalama etkinliği, verileri bu tablo türüyle geçici bir tabloda kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle Kopyalanmakta olan verileri birleştirebilirler. |Tablo türü adı. |Hayır |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>SQL Server ve ' den veri kopyalamaya yönelik JSON örnekleri
Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Aşağıdaki örneklerde SQL Server ve Azure Blob depolama alanına ve bu kaynaktan veri kopyalama gösterilmektedir. Ancak, veriler, Azure Data Factory ' deki kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir kaynaktan herhangi bir kaynağa **doğrudan** kopyalanabilir.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Örnek: SQL Server verileri Azure Blob 'a kopyalama
Aşağıdaki örnek şunu gösterir:

1. [OnPremisesSqlServer](#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Sqlservertable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [SQLSource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip işlem [hattı](data-factory-create-pipelines.md) .

Örnek, zaman serisi verilerini bir SQL Server tablosundan her saat bir Azure blobuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini kurun. Yönergeler, [Şirket içi konumlar ve bulut makaleleri arasında hareketli verilerde](data-factory-move-data-between-onprem-and-cloud.md) yer alır.

**Bağlı hizmet SQL Server**
```json
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
**Azure Blob depolama bağlı hizmeti**

```json
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
**SQL Server girişi veri kümesi**

Örnek, SQL Server bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiğini varsayar. Tek bir veri kümesi kullanarak aynı veritabanı içinde birden çok tablo üzerinde sorgulama yapabilirsiniz, ancak DataSet 'in tableName typeProperty için tek bir tablo kullanılmalıdır.

"External": "true" ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir Data Factory.

```json
{
  "name": "SqlServerInput",
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
**Azure Blob çıktı veri kümesi**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
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
**Kopyalama etkinliği içeren işlem hattı**

İşlem hattı, bu giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **SQLSource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sqlreaderquery** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
Bu örnekte, SqlSource için **Sqlreaderquery** belirtilir. Kopyalama etkinliği, verileri almak için bu sorguyu SQL Server veritabanı kaynağına göre çalıştırır. Alternatif olarak, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** (saklı yordam parametreler alırsa) belirterek bir saklı yordam belirtebilirsiniz. SqlReaderQuery, giriş veri kümesi tarafından başvurulan veritabanı içindeki birden çok tabloya başvurabilir. Yalnızca DataSet 'in tableName typeProperty olarak ayarlanan tablo ile sınırlı değildir.

SqlReaderQuery veya sqlReaderStoredProcedureName belirtmezseniz, yapı bölümünde tanımlanan sütunlar SQL Server veritabanında çalıştırılacak bir seçme sorgusu oluşturmak için kullanılır. Veri kümesi tanımında yapı yoksa, tablodaki tüm sütunlar seçilir.

SqlSource ve BlobSink tarafından desteklenen özelliklerin listesi için bkz. [SQL kaynak](#sqlsource) bölümü ve [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Örnek: Azure Blobundan SQL Server verileri kopyalama
Aşağıdaki örnek şunu gösterir:

1. [OnPremisesSqlServer](#linked-service-properties)türünde bağlı hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı hizmet.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [Sqlservertable](data-factory-sqlserver-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) ve Sqlsink kullanan kopyalama etkinliğine sahip işlem [hattı](data-factory-create-pipelines.md) .

Örnek, zaman serisi verilerini bir Azure blobundan her saat SQL Server tabloya kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Bağlı hizmet SQL Server**

```json
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
**Azure Blob depolama bağlı hizmeti**

```json
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
**Azure Blob giriş veri kümesi**

Veriler her saat yeni bir bloba alınır (sıklık: saat, Aralık: 1). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün bölümünü ve dosya adını kullanır başlangıç zamanının saat kısmını kullanır. "External": "true" ayarı, Data Factory hizmetine veri kümesinin dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini bildirir.

```json
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
**SQL Server çıktı veri kümesi**

Örnek, SQL Server içinde "MyTable" adlı bir tabloya veri kopyalar. Blob CSV dosyasının içermesini beklediğinizi aynı sayıda sütunla SQL Server tablo oluşturun. Yeni satırlar tabloya her saat eklenir.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Kopyalama etkinliği içeren işlem hattı**

İşlem hattı, bu giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **sqlsink**olarak ayarlanır.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Bağlantı sorunlarını giderme
1. SQL Server uzak bağlantıları kabul edecek şekilde yapılandırın. **SQL Server Management Studio**başlatın, **sunucu**' ya sağ tıklayın ve **Özellikler**' e tıklayın. Listeden **Bağlantılar** ' ı seçin ve **sunucuya uzak bağlantılara izin ver**' i işaretleyin.

    ![Uzak bağlantıları etkinleştir](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Ayrıntılı adımlar için bkz. [Uzaktan erişim sunucu yapılandırma seçeneğini yapılandırma](https://msdn.microsoft.com/library/ms191464.aspx) .
2. **SQL Server Yapılandırma Yöneticisi**başlatın. İstediğiniz örnek için **SQL Server ağ yapılandırması** ' nı GENIŞLETIN ve **MSSQLSERVER protokolleri**' ni seçin. Sağ bölmedeki protokolleri görmeniz gerekir. **TCP/IP ' ye** sağ tıklayıp **Etkinleştir**' i tıklayarak TCP/IP 'yi etkinleştirin.

    ![TCP/IP 'yi etkinleştir](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Bkz. TCP/IP protokolünü etkinleştirmenin ayrıntıları ve alternatif yolları için [sunucu ağ protokolünü etkinleştirme veya devre dışı bırakma](https://msdn.microsoft.com/library/ms191294.aspx) .
3. Aynı pencerede, TCP/IP **Özellikler** penceresini başlatmak için **TCP/IP** ' ye çift tıklayın.
4. **IP adresleri** sekmesine geçin. **ipall** bölümünü görmek için aşağı kaydırın. **TCP bağlantı noktasını**(varsayılan **1433**) göz önünde bulunur.
5. Bu bağlantı noktası üzerinden gelen trafiğe izin vermek için makinede **Windows Güvenlik Duvarı için bir kural** oluşturun.
6. **Bağlantıyı doğrula**: tam nitelikli adı kullanarak SQL Server bağlanmak için, farklı bir makineden SQL Server Management Studio kullanın. Örneğin: "\<makine\>.\<etki alanı\>. Corp.\<Company\>. com, 1433. "

   > [!IMPORTANT]
   > 
   > Ayrıntılı bilgi için bkz. [Şirket içi kaynaklar ile bulut arasında veri taşıma veri yönetimi ağ geçidi](data-factory-move-data-between-onprem-and-cloud.md) .
   > 
   > Bağlantı/ağ geçidi ile ilgili sorunları gidermeye yönelik ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="identity-columns-in-the-target-database"></a>Hedef veritabanındaki kimlik sütunları
Bu bölüm, kimlik sütunu olmayan bir kaynak tablodaki verileri kimlik sütunu olan bir hedef tabloya kopyalayan bir örnek sağlar.

**Kaynak tablo:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Hedef tablo:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Hedef tabloda bir kimlik sütunu olduğuna dikkat edin.

**Kaynak veri kümesi JSON tanımı**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Bir işlem hattının kopyalama etkinliğinde SQL havuzundan saklı yordam çağırma örneği için bkz. [kopyalama etkinliği 'NDE SQL havuzu için saklı yordamı çağırma](data-factory-invoke-stored-procedure-from-copy-activity.md) makalesi.

## <a name="type-mapping-for-sql-server"></a>SQL Server için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi kopyalama etkinliği, aşağıdaki 2 adımlı yaklaşımla kaynak türlerindeki otomatik tür dönüştürmeleri, havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

Verileri SQL Server 'dan & taşırken, SQL türünden .NET türüne ve tam tersi bir şekilde aşağıdaki eşlemeler kullanılır.

Eşleme, ADO.NET için SQL Server veri türü eşlemesi ile aynıdır.

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

## <a name="mapping-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleme
Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Tekrarlanabilir kopya
Verileri SQL Server veritabanına kopyalarken kopyalama etkinliği varsayılan olarak havuz tablosuna veri ekler. Bunun yerine, daha fazla bilgi almak için bkz. [SqlSink 'e yinelenebilir yazma](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) makalesi.

İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
