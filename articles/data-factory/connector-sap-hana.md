---
title: SAP HANA verileri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliği kullanarak SAP HANA verileri desteklenen havuz verileri depolarına kopyalamayı öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/22/2020
ms.openlocfilehash: ce3c1e22dd030c0730bf4d9859591c00860908a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382285"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP HANA verileri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-sap-hana-connector.md)
> * [Güncel sürüm](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, SAP HANA veritabanından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

>[!TIP]
>ADF 'nin SAP veri tümleştirme senaryosunda genel desteğini öğrenmek için, her SAP Bağlayıcısı, karşılaştırma ve kılavuza ayrıntılı giriş ile [Azure Data Factory Teknik İnceleme kullanarak SAP veri tümleştirme](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) konusuna bakın.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu SAP HANA Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

SAP HANA veritabanından, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu SAP HANA Bağlayıcısı şunları destekler:

- SAP HANA veritabanının herhangi bir sürümünden veri kopyalanıyor.
- **Hana bilgi modellerinden** (analitik ve hesaplama görünümleri gibi) ve **satır/sütun tablolarının** verilerini kopyalama.
- **Temel** veya **Windows** kimlik doğrulaması kullanarak verileri kopyalama.
- SAP HANA kaynağından paralel kopyalama. Ayrıntılar için [SAP HANA bölümüne paralel kopyalama](#parallel-copy-from-sap-hana) konusuna bakın.

> [!TIP]
> Verileri **SAP HANA veri** deposuna kopyalamak IÇIN Genel ODBC Bağlayıcısı ' nı kullanın. Ayrıntılar [SAP HANA havuz](#sap-hana-sink) bölümüne bakın. SAP HANA Bağlayıcısı ve ODBC Bağlayıcısı için bağlı hizmetlerin farklı türde olduğunu ve bu nedenle yeniden kullanılmamasını aklınızda olun.

## <a name="prerequisites"></a>Önkoşullar

Bu SAP HANA bağlayıcısını kullanmak için şunları yapmanız gerekir:

- Şirket içinde barındırılan bir Integration Runtime ayarlayın. Ayrıntılar için bkz. [Şirket içinde barındırılan Integration Runtime](create-self-hosted-integration-runtime.md) makalesi.
- SAP HANA ODBC sürücüsünü Integration Runtime makinesine yükler. SAP HANA ODBC sürücüsünü [SAP Software Download Center](https://support.sap.com/swdc) sayfasından indirebilirsiniz. **Windows için SAP HANA** anahtar sözcüğünü kullanarak arama yapın.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler SAP HANA bağlayıcıya özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

SAP HANA bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Saphana** olarak ayarlanmalıdır | Yes |
| Dizisi | **Temel kimlik doğrulaması** veya **Windows kimlik doğrulaması** kullanarak SAP HANA bağlanmak için gereken bilgileri belirtin. Aşağıdaki örneklere bakın.<br>Bağlantı dizesinde, sunucu/bağlantı noktası zorunludur (varsayılan bağlantı noktası 30015 ' dir), temel kimlik doğrulaması kullanılırken Kullanıcı adı ve parola zorunludur. Ek Gelişmiş ayarlar için [SAP HANA ODBC bağlantı özellikleri](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) ' ne bakın.<br/>Parolayı Azure Key Vault de yerleştirebilir ve parola yapılandırmasını bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için [Azure Key Vault makalesinde mağaza kimlik bilgilerini](store-credentials-in-key-vault.md) inceleyin. | Yes |
| userName | Windows kimlik doğrulaması kullanırken kullanıcı adını belirtin. Örnek: `user@domain.com` | No |
| password | Kullanıcı hesabı için parola belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşul](#prerequisites)bölümünde belirtildiği gibi, kendinden konak Integration Runtime gereklidir. |Yes |

**Örnek: temel kimlik doğrulaması kullanma**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Windows kimlik doğrulamasını kullanma**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Aşağıdaki yük ile SAP HANA bağlı hizmeti kullanıyorsanız, hala olduğu gibi desteklenirken, ileri ' yi kullanmanız önerilir.

**Örnek:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm SAP HANA veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

SAP HANA verileri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği: **Saphanatable** olarak ayarlanmalıdır | Yes |
| schema | SAP HANA veritabanındaki şemanın adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |
| tablo | SAP HANA veritabanındaki tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable`Türü belirtilmiş veri kümesi kullanıyorsanız, hala olduğu gibi desteklenir, ancak yeni bir adım ileri kullanmanız önerilir.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm SAP HANA kaynak tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sap-hana-as-source"></a>Kaynak olarak SAP HANA

>[!TIP]
>Veri bölümleme kullanarak SAP HANA verileri verimli bir şekilde almak için [SAP HANA bölümünden paralel kopyadan](#parallel-copy-from-sap-hana) daha fazla bilgi edinin.

SAP HANA verileri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Saphanasource** olarak ayarlanmalıdır | Yes |
| sorgu | SAP HANA örneğinden verileri okumak için SQL sorgusunu belirtir. | Yes |
| partitionOptions | SAP HANA verileri almak için kullanılan veri bölümleme seçeneklerini belirtir. [SAP HANA bölümünden paralel kopyadan](#parallel-copy-from-sap-hana) daha fazla bilgi edinin.<br>İzin verme değerleri: **none**   (varsayılan), **physicalpartitionsoftable**, **SapHanaDynamicRange**. [SAP HANA bölümünden paralel kopyadan](#parallel-copy-from-sap-hana) daha fazla bilgi edinin. `PhysicalPartitionsOfTable` yalnızca bir tablodan veri kopyalanırken ve sorgu olmadığında kullanılabilir. <br>Bir bölüm seçeneği etkinleştirildiğinde (yani, değil `None` ), SAP HANA eşzamanlı olarak veri yükleme için paralellik derecesi [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. | Yanlış |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin.<br>Bölüm seçeneği olduğunda Uygula `SapHanaDynamicRange` . | Yanlış |
| partitionColumnName | Paralel kopya için bölüm tarafından kullanılacak kaynak sütunun adını belirtin. Belirtilmemişse, tablonun dizini veya birincil anahtarı otomatik olarak algılanır ve bölüm sütunu olarak kullanılır.<br>Bölüm seçeneği olduğunda uygulayın  `SapHanaDynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız,  `?AdfHanaDynamicRangePartitionCondition` WHERE yan tümcesinde kanca. [SAP HANA bölümünde paralel kopyalama](#parallel-copy-from-sap-hana) örneğine bakın. | Bölüm kullanılırken Evet `SapHanaDynamicRange` . |
| packetSize | Verilerin birden çok bloğuyla bölüneceği ağ paketi boyutunu (kilobayt olarak) belirtir. Kopyalanacak büyük miktarda veriniz varsa, paket boyutunu artırmak çoğu durumda SAP HANA okuma hızını artırabilir. Paket boyutu ayarlanırken performans testi önerilir. | Hayır.<br>Varsayılan değer 2048 ' dir (2MB). |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

`RelationalSource`Yazılı kopyalama kaynağı kullanıyorsanız, hala olduğu gibi desteklenirken, ileri ' yi kullanmanız önerilir.

## <a name="parallel-copy-from-sap-hana"></a>SAP HANA paralel kopyası

Data Factory SAP HANA Bağlayıcısı, verileri SAP HANA paralel olarak kopyalamak için yerleşik veri bölümlemesini sağlar. Kopyalama etkinliğinin **kaynak** tablosunda veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde Data Factory, verileri bölümlere göre almak için SAP HANA kaynağınıza paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu, SAP HANA verilerin bir kısmını alır.

SAP HANA, özellikle de büyük miktarda veri aldığınızda, verilerin bölümlenmesi ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adı belirtin) yazılması önerilir, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                           | Önerilen ayarlar                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Büyük tablodan tam yük.                        | **Bölüm seçeneği**: tablonun fiziksel bölümleri. <br><br/>Yürütme sırasında Data Factory, belirtilen SAP HANA tablosunun fiziksel bölüm türünü otomatik olarak algılar ve ilgili bölüm stratejisini seçer:<br>- **Aralık bölümleme**: tablo için tanımlanan bölüm sütununu ve bölüm aralıklarını alın, sonra verileri aralığa göre kopyalayın. <br>- **Karma bölümlendirme**: karma bölüm anahtarını bölüm sütunu olarak kullanın, ardından verileri BÖLÜMLEYIP ADF hesaplanan aralıklar temelinde kopyalayın. <br>- Hepsini bir **kez deneme bölümlendirme** veya **bölüm yok**: birincil anahtarı bölüm sütunu olarak kullanın, ardından verileri bölümleyip ADF hesaplanan aralıklar temelinde kopyalayın. |
| Özel bir sorgu kullanarak büyük miktarda veriyi yükleyin. | **Bölüm seçeneği**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: Dinamik Aralık bölümünü uygulamak için kullanılan sütunu belirtin. <br><br>Yürütme sırasında Data Factory, belirtilen bölüm sütununun değer aralıklarını eşit bir şekilde dağıtarak, satırları ayrı bölüm sütun değerleri ve ADF paralel kopyalama ayarı sayısına göre eşit bir şekilde dağıtır `?AdfHanaDynamicRangePartitionCondition` ve ardından her bölüm için bölüm sütunu değer aralığını filtreleyerek yerini alır ve SAP HANA gönderir.<br><br>Birden çok sütunu bölüm sütunu olarak kullanmak istiyorsanız, her bir sütunun değerlerini sorgudaki tek bir sütun olarak birleştirebilir ve ADF 'de, gibi bir bölüm sütunu olarak belirtebilirsiniz `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` . |

**Örnek: bir tablonun fiziksel bölümleri ile sorgulama**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA için veri türü eşlemesi

SAP HANA verileri kopyalarken, SAP HANA veri türlerinden aşağıdaki eşlemeler Azure Data Factory geçici veri türlerini kullanır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

| SAP HANA veri türü | Veri Fabrikası geçici veri türü |
| ------------------ | ------------------------------ |
| ALHAYALI um           | Dize                         |
| BIGıNT             | Int64                          |
| Ý             | Byte []                         |
| BINTEXT            | Dize                         |
| Bun               | Byte []                         |
| BOOL               | Bayt                           |
| CLOB               | Dize                         |
| DATE               | DateTime                       |
| KATEGORI            | Ondalık                        |
| ÇIFT             | Çift                         |
| FLOAT              | Çift                         |
| TAMSAYI            | Int32                          |
| NCLOB              | Dize                         |
| NVARCHAR           | Dize                         |
| GERÇEK SAYI               | Tek                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Dize                         |
| SMALLDECıMAL       | Ondalık                        |
| Small           | Int16                          |
| STGEOMETRYTYPE     | Byte []                         |
| STPOINTTYPE        | Byte []                         |
| TEXT               | Dize                         |
| TIME               | TimeSpan                       |
| Iç            | Bayt                           |
| VARCHAR            | Dize                         |
| TIMESTAMP          | DateTime                       |
| IKILI          | Byte []                         |

## <a name="sap-hana-sink"></a>SAP HANA havuzu

Şu anda SAP HANA Bağlayıcısı havuz olarak desteklenmez, ancak SAP HANA 'e veri yazmak için SAP HANA sürücüyle Genel ODBC bağlayıcısını kullanabilirsiniz. 

Şirket içinde barındırılan Integration Runtime ayarlamak ve önce SAP HANA ODBC sürücüsünü yüklemek için [önkoşulları](#prerequisites) izleyin. Aşağıdaki örnekte gösterildiği gibi SAP HANA veri deponuza bağlanmak için bir ODBC bağlantılı hizmeti oluşturun, ardından veri kümesi oluşturun ve etkinlik havuzunu bir şekilde ODBC türüne kopyalayın. [ODBC Bağlayıcısı](connector-odbc.md) makalesinden daha fazla bilgi edinin.

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
