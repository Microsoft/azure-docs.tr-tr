---
title: Azure Data Factory kullanarak Netezza 'ten veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak Netezza 'tan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 4b12c1f24f389634004de3d487a693bc588a7241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374363"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Netezza 'ten veri kopyalama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, verileri Netezza 'dan kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Makale, kopyalama etkinliğine genel bir bakış sunan [Azure Data Factory kopyalama etkinliği](copy-activity-overview.md)üzerinde oluşturulur.

>[!TIP]
>Netezza 'ten Azure 'a veri geçiş senaryosu için, Şirket [Içi Netezza Server 'Dan Azure 'a veri geçirmek üzere Azure Data Factory kullanma](data-migration-guidance-netezza-azure-sqldw.md)hakkında daha fazla bilgi edinin.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Netezza Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)


Netezza 'ten desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının bir listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).

Netezza Bağlayıcısı kaynaktan paralel kopyalamayı destekler. Ayrıntılar için [Netezza adresinden paralel kopyalama](#parallel-copy-from-netezza) bölümüne bakın.

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar. Bu bağlayıcıyı kullanmak için herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>başlarken

.NET SDK, Python SDK, Azure PowerShell, REST API veya Azure Resource Manager şablonu kullanarak kopyalama etkinliği kullanan bir işlem hattı oluşturabilirsiniz. Kopyalama etkinliğine sahip bir işlem hattı oluşturma hakkında adım adım yönergeler için [kopyalama etkinliği öğreticisine](quickstart-create-data-factory-dot-net.md) bakın.

Aşağıdaki bölümler, Netezza bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanabileceğiniz özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Netezza bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | **Type** özelliği **Netezza** olarak ayarlanmalıdır. | Yes |
| Dizisi | Netezza 'e bağlanmak için bir ODBC bağlantı dizesi. <br/>Ayrıca, Azure Key Vault parolayı yerleştirebilir ve `pwd` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanılır. |No |

Tipik bir bağlantı dizesi `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` . Aşağıdaki tabloda ayarlayabileceğiniz daha fazla özellik açıklanmaktadır:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| SecurityLevel | Sürücünün veri deposuyla bağlantı için kullandığı güvenlik düzeyi. Sürücü SSL sürüm 3 kullanılarak tek yönlü kimlik doğrulamasıyla SSL bağlantılarını destekler. <br>Örnek: `SecurityLevel=preferredSecured`. Desteklenen değerler şunlardır:<br/>- **Yalnızca güvenli olmayan** (yalnızca güvenli **olmayan**): sürücü SSL kullanmaz.<br/>- **Tercih edilen güvenli olmayan (Preferredgüvensiz) (varsayılan)**: sunucu bir seçenek sağlıyorsa, sürücü SSL kullanmaz. <br/>- **Tercih edilen güvenli (Preferredgüvenli)**: sunucu bir seçenek sağlıyorsa, sürücü SSL kullanır. <br/>- **Yalnızca güvenli (yalnızca güvenli)**: bir SSL bağlantısı yoksa sürücü bağlanamaz. | No |
| CaCertFile | Sunucu tarafından kullanılan SSL sertifikasının tam yolu. Örnek: `CaCertFile=<cert path>;`| Evet, SSL etkinse |

**Örnek**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Azure Key Vault parola depola**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Bu bölüm, Netezza veri kümesinin desteklediği özelliklerin bir listesini sağlar.

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md).

Netezza öğesinden veri kopyalamak için, veri kümesinin **Type** özelliğini **NetezzaTable** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **NetezzaTable** olarak ayarlanmalıdır | Yes |
| schema | Şemanın adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablo | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Şemanın bulunduğu tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. `schema` `table` Yeni iş yükü için ve kullanın. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Bu bölüm, Netezza kaynağının desteklediği özelliklerin bir listesini sağlar.

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Kaynak olarak Netezza

>[!TIP]
>Veri bölümlemesini kullanarak Netezza 'tan verileri verimli bir şekilde yüklemek için, [Netezza bölümünden paralel kopyadan](#parallel-copy-from-netezza) daha fazla bilgi edinin.

Netezza 'ten veri kopyalamak için kopyalama etkinliğindeki **kaynak** türünü **NetezzaSource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının **Type** özelliği **NetezzaSource** olarak ayarlanmalıdır. | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örnek: `"SELECT * FROM MyTable"` | Hayır (veri kümesinde "tableName" belirtilmişse) |
| partitionOptions | Netezza 'ten veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>Değerlere izin ver: **none** (default), **Dataslice** ve **DynamicRange**.<br>Bir bölüm seçeneği etkinleştirildiğinde (yani, `None` ), bir Netezza veritabanından eşzamanlı olarak veri yükleme derecesi, [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğinde ayarlanarak denetlenir. | No |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği olmadığında Uygula `None` . | No |
| partitionColumnName | Paralel kopya için Aralık bölümleme tarafından kullanılacak, **tamsayı türünde** kaynak sütunun adını belirtin. Belirtilmemişse, tablonun birincil anahtarı oto algılanır ve bölüm sütunu olarak kullanılır. <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız, `?AdfRangePartitionColumnName` WHERE yan tümcesinde kanca. [Netezza from the Parallel Copy](#parallel-copy-from-netezza) bölümüne bakın. | No |
| Partitionüstsınırı | Verilerin kopyalanacağı bölüm sütununun en büyük değeri. <br>Bölüm seçeneği olduğunda Uygula `DynamicRange` . Kaynak verileri almak için sorgu kullanırsanız `?AdfRangePartitionUpbound` WHERE yan tümcesinde kanca. Bir örnek için, [Netezza öğesinden paralel kopyalama](#parallel-copy-from-netezza) bölümüne bakın. | No |
| Partitionalme sınırı | Verilerin kopyalanacağı bölüm sütununun en küçük değeri. <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız, `?AdfRangePartitionLowbound` WHERE yan tümcesinde kanca. Bir örnek için, [Netezza öğesinden paralel kopyalama](#parallel-copy-from-netezza) bölümüne bakın. | No |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Netezza 'den paralel kopya

Data Factory Netezza Bağlayıcısı, verileri Netezza 'den paralel olarak kopyalamak için yerleşik veri bölümlendirme sağlar. Kopyalama etkinliğinin **kaynak** tablosunda veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-netezza/connector-netezza-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde Data Factory, verileri bölümlere göre yüklemek için Netezza kaynağınıza paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu, Netezza veritabanınızdaki verilerin bir kısmını alır.

Netezza veritabanından büyük miktarda veri yüklediğinizde, özellikle veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adını belirt) olarak yazma işlemi geri çağrılır, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Büyük tablodan tam yük.                                   | **Bölüm seçeneği**: veri dilimi. <br><br/>Yürütme sırasında, verileri [Netezza 'in yerleşik veri dilimlerine](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)göre otomatik olarak bölümlere ayırarak Data Factory ve verileri bölümlere göre kopyalar. |
| Özel bir sorgu kullanarak büyük miktarda veriyi yükleyin.                 | **Bölüm seçeneği**: veri dilimi.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>` .<br>Yürütme sırasında, Data Factory `?AdfPartitionCount` (kopyalama etkinliğinde paralel kopyalama numarası kümesiyle) ve `?AdfDataSliceCondition` veri dilimi bölümü mantığı ile, ve Netezza ' e gönderir. |
| Aralık bölümlendirme için eşit olarak dağıtılmış değere sahip bir tamsayı sütunu olan özel bir sorgu kullanarak büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin. Tamsayı veri türünde bir sütuna göre bölümleyebilirsiniz.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı**: yalnızca alt ve üst Aralık arasında veri almak için bölüm sütununa karşı filtrelemek istediğinizi belirtin.<br><br>Yürütme sırasında Data Factory, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` ,, ve `?AdfRangePartitionLowbound` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirilir ve Netezza ' e gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu alt sınır 1 ve üst sınır 80 olarak ayarlarsanız, paralel kopya 4 olarak ayarlandıysa Data Factory verileri 4 bölümden alır. Kimlikleri sırasıyla [1, 20], [21, 40], [41, 60] ve [61, 80] arasındadır. |

**Örnek: veri dilimi bölümüyle sorgulama**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar

Kopyalama etkinliğinin Azure Data Factory kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).
