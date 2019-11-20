---
title: Azure Data Factory kullanarak Teradata Vanndan veri kopyalama
description: Data Factory hizmetinin Teradata Bağlayıcısı, bir Teradata Vanşdan verileri, havuz olarak Data Factory desteklenen veri depolarına kopyalamanızı sağlar.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 4074c50aa17bf804696060134e37055a18bd0137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680099"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Teradata Vanndan veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
>
> * [Sürüm 1](v1/data-factory-onprem-teradata-connector.md)
> * [Geçerli sürüm](connector-teradata.md)

Bu makalede, Teradata vanana 'dan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)sayfasında oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Teradata Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Teradata Vana 'dan verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Teradata Bağlayıcısı şunları destekler:

- Teradata **sürüm 14,10, 15,0, 15,10, 16,0, 16,10 ve 16,20**.
- **Temel** veya **Windows** kimlik doğrulamasını kullanarak verileri kopyalama.
- Teradata kaynağından paralel kopyalama. Ayrıntılar için [Teradata 'Dan paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın.

> [!NOTE]
>
> Şirket içinde barındırılan Integration Runtime v 3.18 yayımlandıktan sonra, Teradata bağlayıcısını Azure Data Factory yükselttiniz. Önceki Teradata bağlayıcısını kullanan tüm mevcut iş yükleri hala desteklenmektedir. Bununla birlikte, yeni iş yükleri için yenisini kullanmak iyi bir fikirdir. Yeni yolun farklı bir bağlı hizmet, veri kümesi ve kopyalama kaynağı kümesi gerektirdiğini unutmayın. Yapılandırma ayrıntıları için aşağıdaki ilgili bölümlere bakın.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime, 3,18 sürümünden başlayarak yerleşik bir Teradata sürücüsü sağlar. Herhangi bir sürücüyü el ile yüklemeniz gerekmez. Sürücü, şirket içinde barındırılan C++ tümleştirme çalışma zamanı makinesinde "Visual Redistributable 2012 güncelleştirme 4" gerektirir. Henüz yüklemediyseniz, [buradan](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)indirin.

3,18 ' den önceki şirket içinde barındırılan tümleştirme çalışma zamanı sürümü için, tümleştirme çalışma zamanı makinesine Teradata, sürüm 14 veya üzeri [için .net veri sağlayıcısı](https://go.microsoft.com/fwlink/?LinkId=278886)sürümünü yüklemelisiniz. 

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Teradata bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Teradata bağlı hizmeti aşağıdaki özellikleri destekler:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği **Teradata**olarak ayarlanmalıdır. | Evet |
| connectionString | Teradata örneğine bağlanmak için gereken bilgileri belirtir. Aşağıdaki örneklere bakın.<br/>Ayrıca Azure Key Vault bir parola yerleştirebilir ve `password` yapılandırmasını bağlantı dizesinden dışarı çekebilirsiniz. Daha ayrıntılı bilgi için [Azure Key Vault 'de mağaza kimlik bilgilerini](store-credentials-in-key-vault.md) inceleyin. | Evet |
| kullanıcı adı | Teradata 'a bağlanmak için bir Kullanıcı adı belirtin. Windows kimlik doğrulaması kullanırken geçerlidir. | Hayır |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabı için bir parola belirtin. Ayrıca, [Azure Key Vault depolanan bir gizli dizi başvurusunu](store-credentials-in-key-vault.md)da seçebilirsiniz. <br>Windows kimlik doğrulaması kullandığınızda veya temel kimlik doğrulaması için Key Vault bir parolaya başvurulduğunda geçerlidir. | Hayır |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Evet |

Büyük/küçük harf olarak bağlantı dizesinde ayarlayabileceğiniz daha fazla bağlantı özelliği:

| Özellik | Açıklama | Varsayılan değer |
|:--- |:--- |:--- |
| CharacterSet | Oturum için kullanılacak karakter kümesi. Ör. `CharacterSet=UTF16`.<br><br/>Bu değer, Kullanıcı tanımlı bir karakter kümesi veya aşağıdaki önceden tanımlanmış karakter kümelerinden biri olabilir: <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>-Shift-JıS (Windows, DOS uyumlu, KANJISJIS_0S)<br/>-EUC (UNIX uyumlu, KANJIEC_0U)<br/>-IBM ana bilgisayar (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>-Networkkorece (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | Varsayılan değer `ASCII`. |
| MaxRespSize |SQL istekleri için en büyük yanıt arabelleği boyutu (kilobayt (KBs) cinsinden). Ör. `MaxRespSize=‭10485760‬`.<br/><br/>Teradata veritabanı sürüm 16,00 veya üzeri için en büyük değer 7361536 ' dir. Önceki sürümleri kullanan bağlantılarda en büyük değer 1048576 ' dir. | Varsayılan değer `65536`. |

**Temel kimlik doğrulaması kullanan örnek**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Windows kimlik doğrulamasını kullanan örnek**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Aşağıdaki yük hala destekleniyor. Bununla birlikte, yeni bir tane kullanmanız gerekir.

**Önceki yük:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Bu bölüm, Teradata veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar. Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md).

Teradata 'tan veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği `TeradataTable`olarak ayarlanmalıdır. | Evet |
| veritabanı | Teradata örneğinin adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |
| tablosundan | Teradata örneğindeki tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` türü veri kümesi hala destekleniyor. Ancak, yeni veri kümesini kullanmanızı öneririz.

**Önceki yük:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Bu bölüm, Teradata kaynağı tarafından desteklenen özelliklerin bir listesini sağlar. Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Kaynak olarak Teradata

>[!TIP]
>Veri bölümleme kullanarak Teradata 'tan verileri verimli bir şekilde yüklemek için [Teradata 'Den paralel kopyadan](#parallel-copy-from-teradata) daha fazla bilgi edinin.

Teradata 'tan veri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği `TeradataSource`olarak ayarlanmalıdır. | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. `"SELECT * FROM MyTable"` bunun bir örneğidir.<br>Bölümlenmiş yükü etkinleştirdiğinizde, sorgunuza karşılık gelen yerleşik bölüm parametrelerini de eklemeniz gerekir. Örnekler için, [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın. | Hayır (veri kümesindeki tablo belirtilmişse) |
| partitionOptions | Teradata 'tan veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>Değerlere izin ver: **none** (varsayılan), **hash** ve **DynamicRange**.<br>Bir bölüm seçeneği etkinleştirildiğinde (`None`değil), Teradata 'den eşzamanlı olarak veri yükleme ile paralellik derecesi kopyalama etkinliğindeki [`parallelCopies`](copy-activity-performance.md#parallel-copy) ayarı tarafından denetlenir. | Hayır |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği `None`olmadığında Uygula. | Hayır |
| partitionColumnName | Paralel kopya için Aralık bölümü veya karma bölümü tarafından kullanılacak kaynak sütunun adını belirtin. Belirtilmemişse, tablonun birincil dizini otomatik olarak algılanır ve bölüm sütunu olarak kullanılır. <br>Bölüm seçeneği `Hash` veya `DynamicRange`olduğunda geçerlidir. Kaynak verileri almak için bir sorgu kullanıyorsanız, ' ın WHERE yan tümcesinde `?AdfHashPartitionCondition` veya `?AdfRangePartitionColumnName`. [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümündeki örneğe bakın. | Hayır |
| partitionüstsınırı | Verilerin kopyalanacağı bölüm sütununun en büyük değeri. <br>Bölüm seçeneği `DynamicRange`olduğunda Uygula. Kaynak verileri almak için sorgu kullanıyorsanız, `?AdfRangePartitionUpbound` WHERE yan tümcesinde kanca. Bir örnek için, [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın. | Hayır |
| Partitionalme sınırı | Verilerin kopyalanacağı bölüm sütununun en küçük değeri. <br>Bölüm seçeneği `DynamicRange`olduğunda geçerlidir. Kaynak verileri almak için bir sorgu kullanırsanız, ' ın WHERE yan tümcesinde `?AdfRangePartitionLowbound` kanca. Bir örnek için, [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın. | Hayır |

> [!NOTE]
>
> `RelationalSource` türü kopyalama kaynağı hala desteklenmektedir, ancak Teradata 'tan yeni yerleşik paralel yüklemeyi (Bölüm seçenekleri) desteklemez. Ancak, yeni veri kümesini kullanmanızı öneririz.

**Örnek: bölüm olmadan temel sorgu kullanarak veri kopyalama**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Teradata 'dan paralel kopya

Data Factory Teradata Bağlayıcısı, Teradata 'den paralel olarak veri kopyalamak için yerleşik veri bölümlendirme sağlar. Kopyalama etkinliğinin **kaynak** tablosunda veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-teradata/connector-teradata-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde Data Factory, verileri bölümlere göre yüklemek için Teradata kaynağınıza karşı paralel sorgular çalıştırır. Paralel derece kopyalama etkinliğinde [`parallelCopies`](copy-activity-performance.md#parallel-copy) ayarıyla denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu, Teradata 'ınızdan verilerin bir kısmını alır.

Özellikle, Teradata 'ınızdan büyük miktarda veri yüklediğinizde, veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adını belirt) olarak yazma işlemi geri çağrılır, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Büyük tablodan tam yük.                                   | **Bölüm seçeneği**: karma. <br><br/>Yürütme sırasında, Data Factory otomatik olarak PK sütununu algılar, buna karşı bir karma uygular ve verileri bölümlere göre kopyalar. |
| Özel bir sorgu kullanarak büyük miktarda veriyi yükleyin.                 | **Bölüm seçeneği**: karma.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Bölüm sütunu**: karma bölüm uygulamak için kullanılan sütunu belirtin. Belirtilmezse, Data Factory Teradata veri kümesinde belirttiğiniz tablonun PK sütununu otomatik olarak algılar.<br><br>Yürütme sırasında, Data Factory `?AdfHashPartitionCondition` karma bölüm mantığı ile değiştirir ve Teradata 'a gönderir. |
| Aralık bölümlendirme için eşit olarak dağıtılmış değere sahip bir tamsayı sütunu olan özel bir sorgu kullanarak büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin. Tamsayı veri türünde bir sütuna göre bölümleyebilirsiniz.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı**: yalnızca alt ve üst Aralık arasında veri almak için bölüm sütununa karşı filtrelemek istediğinizi belirtin.<br><br>Yürütme sırasında, Data Factory `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`ve `?AdfRangePartitionLowbound` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirir ve Teradata 'a gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu alt sınır 1 ve üst sınır 80 olarak ayarlarsanız, paralel kopya 4 olarak ayarlandıysa Data Factory verileri 4 bölümden alır. Kimlikleri sırasıyla [1, 20], [21, 40], [41, 60] ve [61, 80] arasındadır. |

**Örnek: karma bölüm ile sorgulama**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata için veri türü eşlemesi

Teradata 'tan veri kopyaladığınızda aşağıdaki eşlemeler geçerlidir. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| Teradata veri türü | Data Factory geçici veri türü |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte [] |
| Bayt |Byte [] |
| Byteınt |Int16 |
| Char |Dize |
| CLOB |Dize |
| Tarih |DateTime |
| Kategori |Kategori |
| Çift |Çift |
| Sel |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Tamsayı |Int32 |
| Aralık günü |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık gün-saat |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık gün-dakika |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık gün-saniye |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saati |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saat-dakika |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saat-saniye |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık dakikası |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık dakika-saniye |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık ayı |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saniye |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık yılı |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık yıl-ay |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Sayı |Çift |
| Süre (Tarih) |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (saat) |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (saat dilimiyle birlikte) |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (zaman damgası) |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (saat dilimiyle zaman damgası) |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Small |Int16 |
| Zaman |TimeSpan |
| Saat dilimiyle saat |TimeSpan |
| Zaman damgası |DateTime |
| Saat dilimi Ile zaman damgası |DateTime |
| VarByte |Byte [] |
| VarChar |Dize |
| VarGraphic |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| 'Sini |Desteklenmiyor. Kaynak sorgusunda açık tür dönüştürme uygulayın. |


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
