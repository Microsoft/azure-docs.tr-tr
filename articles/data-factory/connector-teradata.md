---
title: Azure Data Factory kullanarak Teradata Vanndan veri kopyalama
description: Data Factory hizmetinin Teradata Bağlayıcısı, bir Teradata Vanşdan verileri, havuz olarak Data Factory desteklenen veri depolarına kopyalamanızı sağlar.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: jingwang
ms.openlocfilehash: c65b295dca16a3453b2ee5472b9a3ee5e8d62df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381775"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Teradata Vanndan veri kopyalama

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
>
> * [Sürüm 1](v1/data-factory-onprem-teradata-connector.md)
> * [Güncel sürüm](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Teradata vanana 'dan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)sayfasında oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Teradata Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Teradata Vana 'dan verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Teradata Bağlayıcısı şunları destekler:

- Teradata **sürüm 14,10, 15,0, 15,10, 16,0, 16,10 ve 16,20**.
- **Temel**, **Windows** veya **LDAP** kimlik doğrulaması kullanarak verileri kopyalama.
- Teradata kaynağından paralel kopyalama. Ayrıntılar için [Teradata 'Dan paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Şirket içinde barındırılan Integration Runtime kullanıyorsanız, sürüm 3,18 ' den başlayarak yerleşik bir Teradata sürücüsü sağlar. Herhangi bir sürücüyü el ile yüklemeniz gerekmez. Sürücü, şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde "Visual C++ Redistributable 2012 güncelleştirme 4" gerektirir. Henüz yüklemediyseniz, [buradan](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)indirin.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Teradata bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Teradata bağlı hizmeti aşağıdaki özellikleri destekler:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **Teradata** olarak ayarlanmalıdır. | Yes |
| Dizisi | Teradata örneğine bağlanmak için gereken bilgileri belirtir. Aşağıdaki örneklere bakın.<br/>Ayrıca Azure Key Vault bir parola yerleştirebilir ve `password` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için [Azure Key Vault 'de mağaza kimlik bilgilerini](store-credentials-in-key-vault.md) inceleyin. | Yes |
| username | Teradata 'a bağlanmak için bir Kullanıcı adı belirtin. Windows kimlik doğrulaması kullanırken geçerlidir. | No |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabı için bir parola belirtin. Ayrıca, [Azure Key Vault depolanan bir gizli dizi başvurusunu](store-credentials-in-key-vault.md)da seçebilirsiniz. <br>Windows kimlik doğrulaması kullandığınızda veya temel kimlik doğrulaması için Key Vault bir parolaya başvurulduğunda geçerlidir. | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

Büyük/küçük harf olarak bağlantı dizesinde ayarlayabileceğiniz daha fazla bağlantı özelliği:

| Özellik | Açıklama | Varsayılan değer |
|:--- |:--- |:--- |
| TdmstPortNumber | Teradata veritabanına erişmek için kullanılan bağlantı noktası numarası.<br>Bu değeri, teknik destek tarafından istenmedikçe değiştirmeyin. | 1025 |
| UseDataEncryption | Teradata veritabanıyla iletişimin tümünün şifrelenip şifrelenmeyeceğini belirtir. İzin verilen değerler 0 veya 1 ' dir.<br><br/>- **0 (devre dışı, varsayılan)**: yalnızca kimlik doğrulama bilgilerini şifreler.<br/>- **1 (etkin)**: sürücü ve veritabanı arasında geçirilen tüm verileri şifreler. | `0` |
| CharacterSet | Oturum için kullanılacak karakter kümesi. Ör., `CharacterSet=UTF16` .<br><br/>Bu değer, Kullanıcı tanımlı bir karakter kümesi veya aşağıdaki önceden tanımlanmış karakter kümelerinden biri olabilir: <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>-LATIN1252_0A<br/>-LATIN9_0A<br/>-LATIN1_0A<br/>-Shift-JıS (Windows, DOS uyumlu, KANJISJIS_0S)<br/>-EUC (UNIX ile uyumlu, KANJIEC_0U)<br/>-IBM ana bilgisayar (KANJIEBCDIC5035_0I)<br/>-KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>-SCHINESE936_6R0<br/>-TCHINESE950_8R0<br/>-Networkkorece (HANGULKSC5601_2R4)<br/>-HANGUL949_7R0<br/>-ARABIC1256_6A0<br/>-CYRILLIC1251_2A0<br/>-HEBREW1255_5A0<br/>-LATIN1250_1A0<br/>-LATIN1254_7A0<br/>-LATIN1258_8A0<br/>-THAI874_4A0 | `ASCII` |
| MaxRespSize |SQL istekleri için en büyük yanıt arabelleği boyutu (kilobayt (KBs) cinsinden). Ör., `MaxRespSize=‭10485760‬` .<br/><br/>Teradata veritabanı sürüm 16,00 veya üzeri için en büyük değer 7361536 ' dir. Önceki sürümleri kullanan bağlantılarda en büyük değer 1048576 ' dir. | `65536` |
| MechanismName | Bağlantının kimliğini doğrulamak üzere LDAP protokolünü kullanmak için, belirtin `MechanismName=LDAP` . | Yok |

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

**LDAP kimlik doğrulaması kullanan örnek**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
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
| tür | Veri kümesinin Type özelliği olarak ayarlanmalıdır `TeradataTable` . | Yes |
| database | Teradata örneğinin adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |
| tablo | Teradata örneğindeki tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

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
> `RelationalTable` tür veri kümesi hala destekleniyor. Ancak, yeni veri kümesini kullanmanızı öneririz.

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
| tür | Kopyalama etkinliği kaynağının Type özelliği olarak ayarlanmalıdır `TeradataSource` . | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. `"SELECT * FROM MyTable"` bunun bir örneğidir.<br>Bölümlenmiş yükü etkinleştirdiğinizde, sorgunuza karşılık gelen yerleşik bölüm parametrelerini de eklemeniz gerekir. Örnekler için, [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın. | Hayır (veri kümesindeki tablo belirtilmişse) |
| partitionOptions | Teradata 'tan veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>Değerlere izin ver: **none** (varsayılan), **hash** ve **DynamicRange**.<br>Bir bölüm seçeneği etkin olduğunda (yani, `None` ), Teradata 'den eşzamanlı olarak veri yükleme için paralellik derecesi [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. | No |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği olmadığında Uygula `None` . | No |
| partitionColumnName | Paralel kopya için Aralık bölümü veya karma bölümü tarafından kullanılacak kaynak sütunun adını belirtin. Belirtilmemişse, tablonun birincil dizini yeniden algılanır ve bölüm sütunu olarak kullanılır. <br>Bölüm seçeneği veya olduğunda geçerlidir `Hash` `DynamicRange` . Kaynak verileri, Hook `?AdfHashPartitionCondition` veya WHERE yan tümcesini almak için bir sorgu kullanırsanız  `?AdfRangePartitionColumnName` . [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümündeki örneğe bakın. | No |
| Partitionüstsınırı | Verilerin kopyalanacağı bölüm sütununun en büyük değeri. <br>Bölüm seçeneği olduğunda Uygula `DynamicRange` . Kaynak verileri almak için sorgu kullanırsanız `?AdfRangePartitionUpbound` WHERE yan tümcesinde kanca. Bir örnek için, [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın. | No |
| Partitionalme sınırı | Verilerin kopyalanacağı bölüm sütununun en küçük değeri. <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız, `?AdfRangePartitionLowbound` WHERE yan tümcesinde kanca. Bir örnek için, [Teradata 'Den paralel kopyalama](#parallel-copy-from-teradata) bölümüne bakın. | No |

> [!NOTE]
>
> `RelationalSource` tür kopyalama kaynağı hala destekleniyor, ancak Teradata 'den yeni yerleşik paralel yükü (Bölüm seçenekleri) desteklemiyor. Ancak, yeni veri kümesini kullanmanızı öneririz.

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

Bölümlenmiş kopyayı etkinleştirdiğinizde Data Factory, verileri bölümlere göre yüklemek için Teradata kaynağınıza karşı paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu, Teradata 'ınızdan verilerin bir kısmını alır.

Özellikle, Teradata 'ınızdan büyük miktarda veri yüklediğinizde, veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adını belirt) olarak yazma işlemi geri çağrılır, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Büyük tablodan tam yük.                                   | **Bölüm seçeneği**: karma. <br><br/>Yürütme sırasında, Data Factory otomatik olarak birincil dizin sütununu algılar, buna karşı bir karma uygular ve verileri bölümlere göre kopyalar. |
| Özel bir sorgu kullanarak büyük miktarda veriyi yükleyin.                 | **Bölüm seçeneği**: karma.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: karma bölüm uygulamak için kullanılan sütunu belirtin. Belirtilmezse, Data Factory Teradata veri kümesinde belirttiğiniz tablonun PK sütununu otomatik olarak algılar.<br><br>Yürütme sırasında Data Factory, `?AdfHashPartitionCondition` karma bölüm mantığı ile değiştirilir ve Teradata 'a gönderir. |
| Aralık bölümlendirme için eşit olarak dağıtılmış değere sahip bir tamsayı sütunu olan özel bir sorgu kullanarak büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin. Tamsayı veri türünde bir sütuna göre bölümleyebilirsiniz.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı**: yalnızca alt ve üst Aralık arasında veri almak için bölüm sütununa karşı filtrelemek istediğinizi belirtin.<br><br>Yürütme sırasında Data Factory, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` ,, ve `?AdfRangePartitionLowbound` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirilir ve Teradata 'a gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu alt sınır 1 ve üst sınır 80 olarak ayarlarsanız, paralel kopya 4 olarak ayarlandıysa Data Factory verileri 4 bölümden alır. Kimlikleri sırasıyla [1, 20], [21, 40], [41, 60] ve [61, 80] arasındadır. |

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
| Ondalık |Ondalık |
| Çift |Çift |
| Sel |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Tamsayı |Int32 |
| Aralık günü |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık gün-saat |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık gün-dakika |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık gün-saniye |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saati |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saat-dakika |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saat-saniye |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık dakikası |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık dakika-saniye |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık ayı |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık saniye |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık yılı |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Aralık yıl-ay |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Sayı |Çift |
| Süre (Tarih) |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (saat) |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (saat dilimiyle birlikte) |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (zaman damgası) |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Süre (saat dilimiyle zaman damgası) |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Small |Int16 |
| Saat |TimeSpan |
| Saat dilimiyle saat |TimeSpan |
| Timestamp |DateTime |
| Saat dilimi Ile zaman damgası |DateTime |
| VarByte |Byte [] |
| VarChar |Dize |
| VarGraphic |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |
| Xml |Desteklenmez. Kaynak sorgusunda açık tür dönüştürme uygulayın. |


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
