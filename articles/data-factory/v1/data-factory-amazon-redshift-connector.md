---
title: Azure Data Factory kullanarak Amazon Redshift 'tan veri taşıma
description: Azure Data Factory kopyalama etkinliğini kullanarak Amazon Redshift 'tan veri taşımayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 707061f523e5e991c851abfe7960a9aa66fb2066
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683258"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Azure Data Factory kullanarak Amazon Redshift 'Tan veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-amazon-redshift-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-amazon-redshift.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de Amazon Redshift Connector](../connector-amazon-redshift.md)bölümüne bakın.

Bu makalede, Amazon Redshift 'tan verileri taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Makale, kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Data Factory Şu anda yalnızca Amazon Redshift 'ten [desteklenen bir havuz veri deposuna](data-factory-data-movement-activities.md#supported-data-stores-and-formats)veri taşımayı destekliyor. Diğer veri depolarından Amazon Redshift 'a veri taşıma desteklenmiyor.

> [!TIP]
> Amazon Redshift 'den büyük miktarlarda veri kopyalarken en iyi performansı elde etmek için, Amazon Simple Storage Service (Amazon S3) aracılığıyla yerleşik Redshift **Unload** komutunu kullanmayı düşünün. Ayrıntılar için bkz. [Amazon Redshift 'tan veri kopyalamak IÇIN kaldırma kullanma](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Ön koşullar
* Verileri şirket içi veri deposuna taşıyorsanız, şirket içi bir makineye [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) yükleyebilirsiniz. Şirket içi makine IP adresini kullanarak Amazon Redshift kümesine bir ağ geçidi için erişim izni verin. Yönergeler için bkz. [kümeye erişim yetkisi verme](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Verileri bir Azure veri deposuna taşımak için, [Microsoft Azure veri merkezleri tarafından kullanılan Işlem IP adresi ve SQL aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)bakın.

## <a name="getting-started"></a>Başlarken
Farklı araçlar ve API 'Ler kullanarak bir Amazon Redshift kaynağından veri taşımak için kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu Azure Data Factory kopyalama Sihirbazı ' nı kullanmaktır. Kopyalama Sihirbazı 'nı kullanarak bir işlem hattı oluşturmaya yönelik hızlı bir anlatım için, bkz [: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md).

Ayrıca, Visual Studio, Azure PowerShell veya diğer araçları kullanarak bir işlem hattı da oluşturabilirsiniz. Azure Resource Manager şablonları, .NET API 'SI veya REST API ardışık düzeni oluşturmak için de kullanılabilir. Kopyalama etkinliği ile işlem hattı oluşturmaya yönelik adım adım yönergeler için, [kopyalama etkinliği öğreticisine](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)bakın.

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için bağlı hizmetler oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem hattı oluşturun.

Kopyalama Sihirbazı 'nı kullandığınızda, bu Data Factory varlıkların JSON tanımları otomatik olarak oluşturulur. Araçları veya API 'Leri (.NET API hariç) kullandığınızda, Data Factory varlıklarını JSON biçimini kullanarak tanımlarsınız. JSON örneği: Amazon Redshift 'dan Azure Blob depolama 'ya veri kopyalama bir Amazon Redshift veri deposundan veri kopyalamak için kullanılan Data Factory varlıkların JSON tanımlarını gösterir.

Aşağıdaki bölümlerde, Amazon Redshift için Data Factory varlıklarını tanımlamak üzere kullanılan JSON özellikleri açıklanır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki tabloda, Amazon Redshift bağlı hizmetine özgü JSON öğeleri için açıklamalar sağlanmaktadır.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| **type** |Bu özellik **AmazonRedshift**olarak ayarlanmalıdır. |Evet |
| **Server** |Amazon Redshift sunucusunun IP adresi veya ana bilgisayar adı. |Evet |
| **bağ** |Amazon Redshift sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası sayısı. |Hayır (varsayılan değer 5439) |
| **veritabanınızı** |Amazon Redshift veritabanının adı. |Evet |
| **nitelen** |Veritabanına erişimi olan kullanıcının adı. |Evet |
| **parolayı** |Kullanıcı hesabının parolası. |Evet |

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. **Yapı**, **kullanılabilirlik**ve **ilke** bölümleri tüm veri kümesi türleri için benzerdir. Veri kümesi türlerine örnek olarak Azure SQL, Azure Blob depolama ve Azure Tablo depolama verilebilir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve depodaki verilerin konumu hakkında bilgi sağlar. Amazon Redshift veri kümesini içeren **Relationaltable**türünde bir veri kümesinin **typeproperties** bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| **tableName** |Amazon Redshift veritabanındaki, bağlı hizmetin başvurduğu tablonun adı. |Hayır ( **Relationalsource** türünde kopyalama etkinliğinin **sorgu** özelliği belirtilmişse) |

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. **Ad**, **Açıklama**, **giriş** tablosu, **Çıkış** tablosu ve **ilke** özellikleri tüm etkinlik türleri için kullanılabilir. **Typeproperties** bölümünde kullanılabilen özellikler her etkinlik türü için farklılık gösterir. Kopyalama etkinliği için özellikler veri kaynağı ve havuz türlerine göre değişir.

Kopyalama etkinliği için, kaynak **AmazonRedshiftSource**türünde olduğunda aşağıdaki özellikler **typeproperties** bölümünde mevcuttur:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| **sorgulayamadı** | Verileri okumak için özel sorguyu kullanın. |Hayır (bir veri kümesinin **TableName** özelliği belirtilmişse) |
| **Redkaydırıcı Tunloadsettings** | Redshift **Unload** komutu kullanılırken Özellik grubunu içerir. | Hayır |
| **s3LinkedServiceName** | Bir ara mağaza olarak kullanmak için Amazon S3. Bağlı hizmet, **Awsaccesskey**türünde bir Azure Data Factory adı kullanılarak belirtilir. | **Redkaydırıcı Tunloadsettings** özelliği kullanılırken gereklidir |
| **bucketName** | Ara verileri depolamak için kullanılacak Amazon S3 demet öğesini gösterir. Bu özellik sağlanmazsa, kopyalama etkinliği otomatik olarak bir demet oluşturur. | **Redkaydırıcı Tunloadsettings** özelliği kullanılırken gereklidir |

Alternatif olarak, **Typeproperties** bölümünde aşağıdaki özelliği Içeren Amazon Redshift Içeren **relationalsource** türünü de kullanabilirsiniz. Bu kaynak türü Redshift **Unload** komutunu desteklemez.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| **sorgulayamadı** |Verileri okumak için özel sorguyu kullanın. | Hayır (bir veri kümesinin **TableName** özelliği belirtilmişse) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Amazon Redshift 'tan veri kopyalamak için KALDıRMA kullanma

Amazon Redshift [**Unload**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) komutu bir sorgunun sonuçlarını Amazon S3 üzerindeki bir veya daha fazla dosyaya kaldırır. Bu komut, Redshift ' den büyük veri kümelerini kopyalamak için Amazon tarafından önerilir.

**Örnek: Amazon Redshift 'tan Azure SQL veri ambarı 'na veri kopyalama**

Bu örnek, Amazon Redshift 'tan Azure SQL veri ambarı 'na veri kopyalar. Örnek, Redshift **Unload** komutunu, hazırlanan kopyalama verilerini ve Microsoft PolyBase 'i kullanır.

Bu örnek kullanım örneği için, kopyalama etkinliği önce Amazon Redshift ' dan Amazon S3 ' dan, **Redkaydırıcı Tunloadsettings** seçeneğinde yapılandırıldığı gibi verileri kaldırır. Daha sonra, veriler, **Stagingsettings** seçeneğinde belirtildiği gibi Amazon S3 öğesinden Azure Blob depolama alanına kopyalanır. Son olarak, PolyBase verileri SQL veri ambarı 'na yükler. Tüm ara biçimler kopyalama etkinliği tarafından işlenir.

![Amazon Redshift 'dan SQL veri ambarı 'na iş akışını kopyalama](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON örneği: Amazon Redshift 'tan Azure Blob depolama alanına veri kopyalama
Bu örnek, bir Amazon Redshift veritabanından Azure Blob depolama alanına nasıl veri kopyalanacağını gösterir. Veriler, kopyalama etkinliği kullanılarak, desteklenen herhangi bir [havuza](data-factory-data-movement-activities.md#supported-data-stores-and-formats) doğrudan kopyalanabilir.

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

* [AmazonRedshift](#linked-service-properties) türünde bağlı bir hizmet
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [Relationaltable](#dataset-properties) türünde bir giriş [veri kümesi](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) türünde bir çıkış [veri kümesi](data-factory-create-datasets.md)
* [Relationalsource](#copy-activity-properties) ve [blobsink](data-factory-azure-blob-connector.md##copy-activity-properties) özelliklerini kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md)

Örnek, Amazon Redshift 'deki bir sorgu sonucundan verileri saatlik bir Azure blobuna kopyalar. Örnekte kullanılan JSON özellikleri, varlık tanımlarını izleyen bölümlerde açıklanmıştır.

**Amazon Redshift bağlı hizmeti**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure Blob depolama bağlı hizmeti**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon Redshift Input veri kümesi**

**External** özelliği "true" olarak ayarlanır ve bu, veri kümesinin veri fabrikası dışında olduğunu Data Factory hizmetini bilgilendirmek için. Bu özellik ayarı, veri kümesinin veri fabrikasında bir etkinlik tarafından üretilmediğini belirtir. İşlem hattındaki bir etkinlik tarafından üretilmeyen bir giriş veri kümesinde özelliği true olarak ayarlayın.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob çıktı veri kümesi**

**Sıklık** özelliği "Hour" ve **Interval** özelliği 1 olarak ayarlanarak, veriler her saat yeni bir bloba yazılır. Blob için **FolderPath** özelliği dinamik olarak değerlendirilir. Özellik değeri, işlenmekte olan dilimin başlangıç zamanına göre belirlenir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Azure Redshift kaynağına sahip bir işlem hattındaki etkinliği (RelationalSource türü) ve bir Azure Blob havuzunu kopyalama**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir. İşlem hattı her saat çalışacak şekilde zamanlanır. İşlem hattının JSON tanımında, **kaynak** türü **relationalsource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu, son saatten kopyalanacak verileri seçer.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Amazon Redshift için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi kopyalama etkinliği, kaynak türünden havuz türüne otomatik tür dönüştürmeleri gerçekleştirir. Türler iki adımlı bir yaklaşım kullanılarak dönüştürülür:

1. Yerel bir kaynak türünden .NET türüne dönüştürme
2. .NET türünden yerel havuz türüne dönüştürme

Kopyalama etkinliği verileri bir Amazon Redshift türünden .NET türüne dönüştürdüğünde aşağıdaki eşlemeler kullanılır:

| Amazon Redshift türü | .NET türü |
| --- | --- |
| Small |Int16 |
| INTEGER |Int32 |
| BIGıNT |Int64 |
| DECIMAL |Kategori |
| GERÇEK |Tek |
| ÇIFT DUYARLıK |Çift |
| BOOLEAN |Dize |
| CHAR |Dize |
| VARCHAR |Dize |
| DATE |DateTime |
| ILIŞKIN |DateTime |
| METINLERI |Dize |

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunların havuz veri kümesindeki sütunlara nasıl eşlendiğini öğrenmek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>İlişkisel kaynaklardan tekrarlanabilir okuma
İlişkisel bir veri deposundan verileri kopyaladığınızda, istenmeyen sonuçlardan kaçınmak için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda bir dilimi yeniden çalıştırmak için bir veri kümesi için yeniden deneme **ilkesini** de yapılandırabilirsiniz. Dilimin kaç kez yeniden çalıştırıldıklarından bağımsız olarak aynı verilerin okunduğunuzdan emin olun. Ayrıca, dilimi yeniden çalıştırma şeklinden bağımsız olarak aynı verilerin okunup de bulunduğundan emin olun. Daha fazla bilgi için bkz. [ilişkisel kaynaklardan tekrarlanabilir okumalar](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayar
Kopyalama etkinliğinin performansını etkileyen anahtar faktörleri ve [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md)' nda performansı iyileştirme yolları hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliği ile işlem hattı oluşturmaya yönelik adım adım yönergeler için, [kopyalama etkinliği öğreticisine](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)bakın.
