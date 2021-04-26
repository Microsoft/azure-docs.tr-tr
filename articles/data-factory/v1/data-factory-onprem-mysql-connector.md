---
title: Azure Data Factory kullanarak MySQL 'ten veri taşıma
description: Azure Data Factory kullanarak MySQL veritabanından veri taşıma hakkında bilgi edinin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83c39435d2249981a45798ffe0717054fa7b0717
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387334"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Azure Data Factory kullanarak MySQL 'Ten veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-onprem-mysql-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-mysql.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de MySQL Bağlayıcısı](../connector-mysql.md)' na bakın.


Bu makalede, verileri şirket içi bir MySQL veritabanından taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Şirket içi bir MySQL veri deposundan, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca bir MySQL veri deposundan diğer veri depolarına veri taşımayı destekler, ancak verileri diğer veri depolarından MySQL veri deposuna taşımamaktadır. 

## <a name="prerequisites"></a>Önkoşullar
Data Factory hizmeti, Veri Yönetimi ağ geçidini kullanarak şirket içi MySQL kaynaklarına bağlanmayı destekler. Veri Yönetimi ağ geçidini ayarlama hakkında bilgi edinmek ve ağ geçidini ayarlamaya yönelik adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makaleleri arasında veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

MySQL veritabanı bir Azure IaaS sanal makinesinde (VM) barındırılıyorsa bile ağ geçidi gereklidir. Ağ geçidini, veri deposuyla aynı VM 'ye veya ağ geçidinin veritabanına bağlanabildiği sürece farklı bir VM 'ye yükleyebilirsiniz.

> [!NOTE]
> Bağlantı/ağ geçidi ile ilgili sorunları gidermeye yönelik ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Desteklenen sürümler ve yükleme
MySQL veritabanına bağlanmak üzere Veri Yönetimi ağ geçidinin, Veri Yönetimi ağ geçidiyle aynı sisteme [Microsoft Windows Için MySQL bağlayıcısını/net](https://dev.mysql.com/downloads/connector/net/) (6.6.5 ve 6.10.7 arasında sürüm) yüklemeniz gerekir. Bu 32 bit sürücü, 64 bit Veri Yönetimi ağ geçidi ile uyumludur. MySQL sürüm 5,1 ve üzeri desteklenir.

> [!TIP]
> "Uzak taraf aktarım akışını kapattığından" kimlik doğrulaması başarısız oldu. "hatasını alırsanız MySQL bağlayıcısını/NET 'i daha yüksek sürüme yükseltmeyi göz önünde bulundurun.

## <a name="getting-started"></a>Kullanmaya başlama
Farklı araçlar/API 'Ler kullanarak şirket içi Cassandra veri deposundan veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz. 

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) . 
- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API** ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. 
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. 

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Şirket içi MySQL veri deposundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarına sahip bir örnek için, bu makaledeki [JSON örneği: MySQL 'Den Azure Blob 'a veri kopyalama](#json-example-copy-data-from-mysql-to-azure-blob) bölümüne bakın. 

Aşağıdaki bölümler, bir MySQL veri deposuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, MySQL bağlantılı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tür |Type özelliği: **OnPremisesMySql** olarak ayarlanmalıdır |Yes |
| sunucu |MySQL sunucusunun adı. |Yes |
| database |MySQL veritabanının adı. |Yes |
| schema |Veritabanındaki şemanın adı. |No |
| authenticationType |MySQL veritabanına bağlanmak için kullanılan kimlik doğrulaması türü. Olası değerler şunlardır: `Basic` . |Yes |
| userName |MySQL veritabanına bağlanmak için Kullanıcı adını belirtin. |Yes |
| password |Belirttiğiniz kullanıcı hesabı için parola belirtin. |Yes |
| gatewayName |Data Factory hizmetinin şirket içi MySQL veritabanına bağlanmak için kullanması gereken ağ geçidinin adı. |Yes |

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Relationaltable** türündeki veri kümesinin typeproperties bölümü (MySQL veri kümesini içerir) aşağıdaki özelliklere sahiptir

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Bağlantılı hizmetin başvurduğu MySQL veritabanı örneğindeki tablonun adı. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları gibi özellikler, tüm etkinlik türleri için kullanılabilir ilkeleridir.

Ancak, etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Copy etkinliğinin kaynağı **Relationalsource** (MySQL içeren) türünde olduğunda, typeproperties bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL sorgu dizesi. Örneğin: select * from MyTable. |Hayır ( **veri kümesi** **TableName** belirtilmişse) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>JSON örneği: MySQL 'ten Azure Blob 'a veri kopyalama
Bu örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Şirket içi bir MySQL veritabanından bir Azure Blob depolama alanına nasıl veri kopyalanacağını gösterir. Ancak, veriler burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza kopyalanabilir.

> [!IMPORTANT]
> Bu örnek, JSON parçacıkları sağlar. Veri Fabrikası oluşturmaya yönelik adım adım yönergeler içermez. Adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makalesi arasında verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

1. [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
3. [Relationaltable](data-factory-onprem-mysql-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Relationalsource](data-factory-onprem-mysql-connector.md#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, bir sorgudan veri kopyalar MySQL veritabanındaki bir blob 'a saatlik olarak. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

İlk adım olarak, veri yönetimi ağ geçidini kurun. Yönergeler, [Şirket içi konumlar ve bulut makaleleri arasında hareketli verilerde](data-factory-move-data-between-onprem-and-cloud.md) yer alır.

**MySQL bağlı hizmeti:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Azure depolama bağlı hizmeti:**

```JSON
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

**MySQL giriş veri kümesi:**

Örnek, MySQL içinde bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiğini varsayar.

"External": "true" ayarı, Data Factory hizmetine tablonun veri fabrikasında dış olduğunu bildirir ve veri fabrikasında bir etkinlik tarafından üretilmez.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopyalama etkinliği içeren işlem hattı:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü, **relationalsource** olarak ayarlanır ve **Havuz** türü **blobsink** olarak ayarlanır. **Sorgu** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>MySQL için tür eşleme
[Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde belirtildiği gibi, kopyalama etkinliği, kaynak türlerindeki otomatik tür dönüştürmeleri aşağıdaki iki adımlı yaklaşımla birlikte havuz türlerine uygular:

1. Yerel kaynak türlerinden .NET türüne Dönüştür
2. .NET türünden yerel havuz türüne Dönüştür

MySQL 'e veri taşırken aşağıdaki eşlemeler MySQL türlerinden .NET türlerine kullanılır.

| MySQL veritabanı türü | .NET Framework türü |
| --- | --- |
| büyük tamsayı işaretsiz |Ondalık |
| bigint |Int64 |
| bit |Ondalık |
| blob |Byte [] |
| bool |Boole |
| char |Dize |
| date |Tarih saat |
| datetime |Tarih saat |
| decimal |Ondalık |
| çift duyarlık |Çift |
| double |Çift |
| enum |Dize |
| float |Tek |
| int işaretsiz |Int64 |
| int |Int32 |
| tamsayı işaretsiz |Int64 |
| tamsayı |Int32 |
| Long varbinary |Byte [] |
| uzun varchar |Dize |
| LONGBLOB |Byte [] |
| LONGTEXT |Dize |
| düz blob |Byte [] |
| düz int işaretsiz |Int64 |
| düz tamsayı |Int32 |
| düz metin |Dize |
| sayısal |Ondalık |
| real |Çift |
| set |Dize |
| küçük tamsayı işaretsiz |Int32 |
| smallint |Int16 |
| metin |Dize |
| time |TimeSpan |
| timestamp |Tarih saat |
| tinyblob |Byte [] |
| mini tamsayı imzasız |Int16 |
| tinyint |Int16 |
| tinytext |Dize |
| varchar |Dize |
| yıl |int |

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
