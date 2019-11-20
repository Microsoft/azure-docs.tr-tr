---
title: Data Factory kullanarak Oracle 'a veya Oracle 'a veri kopyalama
description: Azure Data Factory kullanarak şirket içi Oracle veritabanına veri kopyalama hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 822713d67790906c972ad77a748ef8d52b871bc4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682433"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Oracle 'a veya şirket içi verileri kopyalama

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-onprem-oracle-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-oracle.md)

> [!NOTE]
> Bu makale, Azure Data Factory’nin 1. sürümü için geçerlidir. Azure Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Oracle Bağlayıcısı](../connector-oracle.md).


Bu makalede, şirket içi Oracle veritabanına veri taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanır. Makale, kopyalama etkinliğini kullanarak veri hareketine genel bir bakış sunan [veri taşıma etkinliklerini](data-factory-data-movement-activities.md)oluşturur.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

*Bir Oracle veritabanından* aşağıdaki veri depolarına veri kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarından verileri *bir Oracle veritabanına*kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Ön koşullar

Data Factory, Veri Yönetimi ağ geçidini kullanarak şirket içi Oracle kaynaklarına bağlanmayı destekler. Veri Yönetimi ağ geçidi hakkında daha fazla bilgi için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) . Verileri taşımak için bir veri ardışık düzeninde ağ geçidini ayarlama hakkında adım adım yönergeler için bkz. [Şirket içinden buluta veri taşıma](data-factory-move-data-between-onprem-and-cloud.md).

Oracle, bir Azure hizmet olarak altyapı (IaaS) sanal makinesi içinde barındırılıyorsa bile ağ geçidi gereklidir. Ağ geçidini veritabanına bağlanabildiği sürece, ağ geçidini veri deposuyla aynı IaaS sanal makinesine veya farklı bir VM 'ye yükleyebilirsiniz.

> [!NOTE]
> Bağlantıyla ve ağ geçidiyle ilgili sorunları gidermeye yönelik ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Desteklenen sürümler ve yükleme

Bu Oracle Bağlayıcısı iki sürücü sürümünü destekler:

- **Oracle Için Microsoft sürücüsü (önerilir)** : veri yönetimi ağ geçidi sürüm 2,7 ' den başlayarak, bir Oracle için Microsoft sürücüsü ağ geçidine göre otomatik olarak yüklenir. Oracle bağlantısı kurmak için sürücüyü yüklemeniz veya güncelleştirmeniz gerekmez. Ayrıca, bu sürücüyü kullanarak daha iyi kopyalama performansı da yaşayabilirsiniz. Oracle veritabanlarının bu sürümleri desteklenir:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10G R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Oracle proxy sunucusu desteklenmez.

    > [!IMPORTANT]
    > Şu anda, Oracle için Microsoft sürücüsü yalnızca Oracle 'dan verileri kopyalamayı destekler. Sürücü Oracle 'a yazmayı desteklemiyor. Veri Yönetimi ağ geçidi **Tanılama** sekmesindeki test bağlantısı özelliği bu sürücüyü desteklemiyor. Alternatif olarak, bağlantıyı doğrulamak için kopyalama Sihirbazı 'nı da kullanabilirsiniz.
    >

- **.Net Için oracle veri sağlayıcısı**: Oracle veri sağlayıcısı kullanarak veya Oracle 'dan veri kopyalayabilirsiniz. Bu bileşen [Windows Için Oracle Data Access Components](https://www.oracle.com/technetwork/topics/dotnet/downloads/)'a dahildir. Ağ geçidinin yüklü olduğu makineye ilgili sürümü (32-bit veya 64-bit) yükleme. [Oracle veri sağlayıcısı .net 12,1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) , 10G sürüm 2 ve sonraki sürümlerine erişebilir Oracle Database.

    **Xcopy yükleme**' yi seçerseniz Readme. htm dosyasında açıklanan adımları uygulayın. Kullanıcı arabirimine (XCopy yükleyicisi değil) sahip yükleyiciyi seçmenizi öneririz.

    Sağlayıcıyı yükledikten sonra, hizmetler uygulamasını veya Veri Yönetimi ağ geçidi Configuration Manager kullanarak makinenizde Veri Yönetimi ağ geçidi konak hizmetini yeniden başlatın.

Kopyalama işlem hattını yazmak için kopyalama Sihirbazı 'nı kullanırsanız, sürücü türü tekrar belirlenir. Ağ geçidiniz sürüm 2,7 ' den daha eski veya havuz olarak Oracle ' ı seçmediğiniz sürece Microsoft sürücüsü varsayılan olarak kullanılır.

## <a name="get-started"></a>Başlarken

Kopyalama etkinliğine sahip bir işlem hattı oluşturabilirsiniz. İşlem hattı, farklı araçları veya API 'Leri kullanarak verileri şirket içi Oracle veritabanına veya sunucudan taşıdıkça.

İşlem hattı oluşturmanın en kolay yolu kopyalama Sihirbazı ' nı kullanmaktır. Veri Kopyalama Sihirbazı 'nı kullanarak bir işlem hattı oluşturmaya yönelik hızlı bir anlatım için [Kopyalama Sihirbazı 'nı kullanarak bir işlem hattı oluşturma öğreticisine](data-factory-copy-data-wizard-tutorial.md) bakın.

Bir işlem hattı oluşturmak için aşağıdaki araçlardan birini de kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**veya **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturma hakkında adım adım yönergeler için [kopyalama etkinliği öğreticisine](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) bakın.

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları izleyin:

1. Bir **Veri Fabrikası**oluşturun. Bir veri fabrikası, bir veya daha fazla işlem hattı içerebilir.
2. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun. Örneğin, bir Oracle veritabanından Azure Blob depolama alanına veri kopyalıyorsanız, Oracle veritabanınızı ve Azure depolama hesabınızı veri fabrikanıza bağlamak için iki bağlı hizmet oluşturun. Oracle 'a özgü bağlantılı hizmet özellikleri için bkz. [bağlı hizmet özellikleri](#linked-service-properties).
3. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. Yukarıdaki adımdaki örnekte, Oracle veritabanınızda giriş verilerini içeren tabloyu belirtmek için bir veri kümesi oluşturursunuz. Blob kapsayıcısını ve Oracle veritabanından kopyalanmış verileri tutan klasörü belirtmek için başka bir veri kümesi oluşturursunuz. Oracle 'a özgü veri kümesi özellikleri için bkz. [DataSet özellikleri](#dataset-properties).
4. Giriş ve veri kümesi olarak bir veri kümesini çıkış olarak alan bir kopyalama etkinliğine sahip bir işlem **hattı** oluşturun. Yukarıdaki örnekte, kopyalama etkinliği için bir havuz olarak **oraclesource** 'u kaynak ve **blobsink** olarak kullanacaksınız. Benzer şekilde, Azure Blob depolama alanından bir Oracle veritabanına kopyalama yapıyorsanız kopyalama etkinliğinde **Blobsource** ve **oraclesink** kullanın. Oracle veritabanına özgü kopyalama etkinliği özellikleri için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties). Bir veri deposunu kaynak veya havuz olarak kullanma hakkında ayrıntılı bilgi için, önceki bölümde veri deponuzu bağlantısını seçin.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları sizin için otomatik olarak oluşturulur: bağlı hizmetler, veri kümeleri ve işlem hattı. Araçlar veya API 'Ler kullandığınızda (.NET API hariç), bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Şirket içi Oracle veritabanına veri kopyalamak için kullandığınız Data Factory varlıkları için JSON tanımlarına sahip örnekler için, bkz. JSON örnekleri.

Aşağıdaki bölümler, Data Factory varlıkları tanımlamak için kullandığınız JSON özellikleri hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki tabloda, Oracle bağlantılı hizmetine özgü JSON öğeleri açıklanmaktadır:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |**Type** özelliği **OnPremisesOracle**olarak ayarlanmalıdır. |Evet |
| driverType | Bir Oracle veritabanından veri kopyalamak için kullanılacak sürücüyü belirtin. İzin verilen değerler **Microsoft** ve **ODP** (varsayılan). Sürücü ayrıntıları için [desteklenen sürüm ve yükleme](#supported-versions-and-installation) bölümüne bakın. | Hayır |
| connectionString | **ConnectionString** özelliği için Oracle Database örneğine bağlanmak için gereken bilgileri belirtin. | Evet |
| gatewayName | Şirket içi Oracle sunucusuna bağlanmak için kullanılan ağ geçidinin adı. |Evet |

**Örnek: Microsoft sürücüsünü kullanma**

> [!TIP]
> "ORA-01025: UPı parametresi aralık dışında" bir hata görürseniz ve Oracle sürümü 8i ise, Bağlantı dizenizi `WireProtocolMode=1` ekleyin ve yeniden deneyin:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Örnek: ODP sürücüsünü kullanma**

İzin verilen biçimler hakkında bilgi edinmek için bkz. [.net ODP Için Oracle veri sağlayıcısı](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md).

Yapı, kullanılabilirlik ve ilke gibi bir veri kümesi JSON dosyasının bölümleri tüm veri kümesi türleri (örneğin, Oracle, Azure Blob depolama ve Azure Tablo Depolaması) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Oracletable** türündeki veri kümesinin **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Oracle veritabanındaki, bağlı hizmetin başvurduğu tablonun adı. |Hayır ( **oracleReaderQuery** veya **oraclesource** belirtilmişse) |

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md).

Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler tüm etkinlik türleri için kullanılabilir.

> [!NOTE]
> Kopyalama etkinliği yalnızca bir girdi alır ve yalnızca bir çıktı üretir.

Etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği özellikleri kaynak ve havuzun türüne göre farklılık gösterir.

### <a name="oraclesource"></a>OracleSource

Kopyalama etkinliğinde, kaynak **Oraclesource** türü olduğunda, **typeproperties** bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| oracleReaderQuery |Verileri okumak için özel sorguyu kullanın. |Bir SQL sorgu dizesi. Örneğin, " **MyTable**içinden \* seçin". <br/><br/>Belirtilmemişse, bu SQL açıklaması yürütülür: " **MyTable**\* seçin" |Hayır<br />( **veri kümesi** **TableName** belirtilmişse) |

### <a name="oraclesink"></a>OracleSink

**Oraclesink** aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| writeBatchTimeout |Toplu iş ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. |**TimeSpan**<br/><br/> Örnek: 00:30:00 (30 dakika) |Hayır |
| writeBatchSize |Arabellek boyutu **Writebatchsize**değerine ULAŞTıĞıNDA verileri SQL tablosuna ekler. |Tamsayı (satır sayısı) |Hayır (varsayılan: 100) |
| sqlWriterCleanupScript |Belirli bir dilim verilerinin temizlenmesi için kopyalama etkinliğinin yürütülmesi için bir sorgu belirtir. |Sorgu ekstresi. |Hayır |
| Dilimleyiceıdentifiercolumnname |Kopyalama etkinliğinin otomatik olarak oluşturulan bir dilim tanımlayıcısı ile doldurmasını sağlamak için sütun adını belirtir. Yeniden çalıştırıldığında belirli bir dilimin verilerini temizlemek için, **Feıdentifiercolumnname** değeri kullanılır. |**Binary (32)** veri türüne sahip bir sütunun sütun adı. |Hayır |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Oracle veritabanına veri kopyalamaya yönelik JSON örnekleri

Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Örneklerde, bir Oracle veritabanından ve Azure Blob depolama alanına ya da veri kopyalama işlemlerinin nasıl yapılacağı gösterilmektedir. Ancak, veriler Azure Data Factory ' de kopyalama etkinliği kullanılarak [desteklenen veri depolarında ve biçimlerde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) listelenen herhangi bir havuza kopyalanabilir.

**Örnek: Oracle 'dan Azure Blob depolama 'ya veri kopyalama**

Örnek aşağıdaki Data Factory varlıklara sahiptir:

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [Oracletable](data-factory-onprem-oracle-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
* Kaynak ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) olarak, havuz olarak [oraclesource](data-factory-onprem-oracle-connector.md#copy-activity-properties) kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, verileri şirket içi Oracle veritabanında bulunan bir tablodan bir bloba saat başı olarak kopyalar. Örnekte kullanılan çeşitli özellikler hakkında daha fazla bilgi için, örnekleri izleyen bölümlere bakın.

**Oracle bağlı hizmeti**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle giriş veri kümesi**

Örnek, Oracle 'da **MyTable** adlı bir tablo oluşturduğunuzu varsayar. Zaman serisi verileri için **timestampcolumn** adlı bir sütun içerir.

**External**: **true** ayarı, veri kümesinin veri fabrikasında dış olduğunu ve veri kümesinin veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
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

Veriler her saat yeni bir bloba yazılır (**Sıklık**: **saat**, **Aralık**: **1**). Blob 'un klasör yolu ve dosya adı, işlenen dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısmını kullanır.

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

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış ve saatlik olarak çalıştırılmak üzere zamanlanmış bir kopyalama etkinliği içerir. Ardışık düzen JSON tanımında **kaynak** türü **oraclesource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **OracleReaderQuery** özelliğini kullanarak belirttiğiniz SQL sorgusu, kopyalamanın Son saatteki verileri seçer.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

**Örnek: Azure Blob depolamadan Oracle 'a veri kopyalama**

Bu örnekte, verileri bir Azure Blob depolama hesabından şirket içi Oracle veritabanına kopyalama gösterilmektedir. Ancak, Azure Data Factory ' deki kopyalama etkinliğini kullanarak, [desteklenen veri depolarında ve biçimlerde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) listelenen kaynaklardan verileri *doğrudan* kopyalayabilirsiniz.

Örnek aşağıdaki Data Factory varlıklara sahiptir:

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
* [Oracletable](data-factory-onprem-oracle-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
* Havuz olarak [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) 'U kaynak [oraclesink](data-factory-onprem-oracle-connector.md#copy-activity-properties) olarak kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, bir Blobun verileri her saatte Şirket içi Oracle veritabanındaki bir tabloya kopyalar. Örnekte kullanılan çeşitli özellikler hakkında daha fazla bilgi için, örnekleri izleyen bölümlere bakın.

**Oracle bağlı hizmeti**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure Blob girdi veri kümesi**

Veriler her saat yeni bir bloba alınır (**Sıklık**: **saat**, **Aralık**: **1**). Blob 'un klasör yolu ve dosya adı, işlenen dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün bölümünü kullanır. Dosya adı başlangıç zamanının saat kısmını kullanır. **External**: **true** ayarı, bu tablonun veri fabrikasında dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory hizmetine bildirir.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
            "frequency": "Day",
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

**Oracle çıkış veri kümesi**

Örnek, Oracle 'da **MyTable** adlı bir tablo oluşturduğunuzu varsayar. Blob CSV dosyasının içermesini beklediğinizi aynı sayıda sütun ile Oracle 'da tablo oluşturun. Yeni satırlar tabloya her saat eklenir.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Kopyalama etkinliği içeren işlem hattı**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış ve her saat çalışacak şekilde zamanlanan bir kopyalama etkinliği içerir. Ardışık düzen JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **oraclesink**olarak ayarlanır.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

### <a name="problem-1-net-framework-data-provider"></a>Sorun 1: .NET Framework Veri Sağlayıcısı

**Hata iletisi**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**Olası nedenler**

* Oracle için .NET Framework Veri Sağlayıcısı yüklenmedi.
* Oracle için .NET Framework Veri Sağlayıcısı .NET Framework 2,0 ' e yüklenmiştir ve .NET Framework 4,0 klasörlerinde bulunamadı.

**Çözünürlüğüne**

* Oracle için .NET sağlayıcısını yüklemediyseniz, [uygulamayı yükleyip](https://www.oracle.com/technetwork/topics/dotnet/downloads/)senaryoyu yeniden deneyin.
* Sağlayıcıyı yükledikten sonra bile hata iletisini görürseniz, aşağıdaki adımları izleyin:
    1. < Sistem diski\>klasöründen .NET 2,0 için makine yapılandırma dosyasını açın: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. **.Net Için Oracle veri sağlayıcısı**aratın. Aşağıdaki örnekte gösterildiği gibi bir girişi, **System. data** > **DbProviderFactory**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />` olarak bulabilirsiniz.
* Bu girdiyi şu .NET 4,0 klasöründeki Machine. config dosyasına kopyalayın: < sistem diski\>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Ardından sürümü 4. xxx. x.x. olarak değiştirin
* **Gacutil/i [Provider Path]** çalıştırarak genel derleme önbelleği 'NE (GAC) < ODP. net yüklü yol\>\11.2.0\client_1\odp.net\bin\4\Oracle.dataaccess.dll.

### <a name="problem-2-datetime-formatting"></a>Sorun 2: Tarih/saat biçimlendirmesi

**Hata iletisi**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Çözünürlüğüne**

Kopyalama etkinliğinizdeki sorgu dizesini, tarihlerin Oracle veritabanınızda nasıl yapılandırıldığına bağlı olarak ayarlamanız gerekebilir. İşte bir örnek ( **to_date** işlevini kullanarak):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Oracle için tür eşleme

[Veri taşıma etkinliklerinde](data-factory-data-movement-activities.md)belirtildiği gibi kopyalama etkinliği, aşağıdaki iki adımlı yaklaşımı kullanarak kaynak türlerinden havuz türlerine otomatik tür dönüştürmeleri gerçekleştirir:

1. Yerel kaynak türlerinden .NET türüne dönüştürün.
2. .NET türünden yerel havuz türüne dönüştürün.

Verileri Oracle 'dan taşıdığınızda, aşağıdaki eşlemeler Oracle veri türünden .NET türüne ve tam tersi şekilde kullanılır:

| Oracle veri türü | .NET Framework veri türü |
| --- | --- |
| BDosya |Byte [] |
| BLOB |Byte []<br/>(yalnızca Microsoft sürücüsü kullandığınızda Oracle 10G ve sonraki sürümlerinde desteklenir) |
| CHAR |Dize |
| CLOB |Dize |
| DATE |DateTime |
| FLOAT |Ondalık, dize (duyarlık > 28) |
| INTEGER |Ondalık, dize (duyarlık > 28) |
| ARALıK YıL-AY |Int32 |
| ARALıK GÜN-SANIYE |TimeSpan |
| KALACAĞıNı |Dize |
| UZUN HAM |Byte [] |
| NCHAR |Dize |
| NCLOB |Dize |
| SAYıSıNDAN |Ondalık, dize (duyarlık > 28) |
| NVARCHAR2 |Dize |
| Madde |Byte [] |
| ROWıD |Dize |
| ILIŞKIN |DateTime |
| YEREL SAAT DILIMIYLE ZAMAN DAMGASı |DateTime |
| SAAT DILIMI ILE ZAMAN DAMGASı |DateTime |
| IŞARETSIZ TAMSAYı |Sayı |
| VARCHAR2 |Dize |
| 'SINI |Dize |

> [!NOTE]
> Bir Microsoft sürücüsü kullandığınızda, veri türleri **Aralık yılı** ve **Aralık gün-saniye** desteklenmez.

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin

Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında daha fazla bilgi için, bkz. [Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma

İlişkisel veri depolarından verileri kopyaladığınızda, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim el ile veya bir yeniden deneme ilkesi tarafından yeniden çalıştırıldığında, dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Daha fazla bilgi için bkz. [ilişkisel kaynaklardan yinelenebilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayar

Azure Data Factory veri taşıma (kopyalama etkinliği) performansını etkileyen anahtar faktörleri hakkında bilgi edinmek için [etkinlik performansını kopyalama ve ayarlama kılavuzuna](data-factory-copy-activity-performance.md) bakın. Ayrıca, bunu iyileştirmek için çeşitli yollar hakkında bilgi edinebilirsiniz.
