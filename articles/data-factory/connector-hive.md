---
title: Azure Data Factory kullanarak Hive 'den veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak Hive 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 8f6e85d82c01663e404f7046f84706feb209ba5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367036"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Azure Data Factory kullanarak Hive 'den veri kopyalama ve dönüştürme 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Hive 'den veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Hive Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Verileri Hive 'dan desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar, bu nedenle bu bağlayıcıyı kullanarak herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Hive bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Hive bağlantılı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Hive** olarak ayarlanmalıdır | Yes |
| konak | Birden çok konak için '; ' ile ayrılmış Hive sunucusunun IP adresi veya ana bilgisayar adı (yalnızca serviceDiscoveryMode etkinleştirildiğinde).  | Yes |
| port | Hive sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. Azure Hdınsights 'e bağlanıyorsanız, bağlantı noktasını 443 olarak belirtin. | Yes |
| serverType | Hive sunucusunun türü. <br/>İzin verilen değerler: **HiveServer1**, **HiveServer2**, **hivethriftserver** | No |
| thriftTransportProtocol | Thrift katmanında kullanılacak Aktarım Protokolü. <br/>İzin verilen değerler şunlardır: **binary**, **SASL**, **http** | No |
| authenticationType | Hive sunucusuna erişmek için kullanılan kimlik doğrulama yöntemi. <br/>İzin verilen değerler: **anonim**, **Kullanıcı adı**, **userNameAndPassWord**, **WindowsAzureHDInsightService**. Kerberos kimlik doğrulaması şu anda desteklenmiyor. | Yes |
| serviceDiscoveryMode | ZooKeeper hizmetinin kullanılması gerektiğini belirtmek için true, false değil.  | No |
| zooKeeperNameSpace | Hive sunucu 2 düğümlerinin eklendiği ZooKeeper üzerindeki ad alanı.  | No |
| useNativeQuery | Sürücünün yerel HiveQL sorguları kullanıp kullanmadığını veya onları HiveQL 'teki eşdeğer bir biçime dönüştürmeyeceğini belirtir.  | No |
| username | Hive sunucusuna erişmek için kullandığınız Kullanıcı adı.  | No |
| password | Kullanıcıya karşılık gelen parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| httpPath | Hive sunucusuna karşılık gelen kısmi URL.  | No |
| enableSsl | Sunucu bağlantılarının TLS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer false'tur.  | No |
| trustedCertPath | TLS üzerinden bağlanılırken sunucuyu doğrulamak için güvenilir CA sertifikaları içeren. ped dosyasının tam yolu. Bu özellik yalnızca, şirket içinde barındırılan IR 'de TLS kullanılırken ayarlanabilir. Varsayılan değer, IR ile birlikte yüklenen CAcert. Pez dosyasıdır.  | No |
| useSystemTrustStore | Sistem güven deposundan veya belirtilen ped dosyasından bir CA sertifikası kullanılıp kullanılmayacağını belirtir. Varsayılan değer false'tur.  | No |
| Allowwhostnamecnuyuşmazlığını | TLS üzerinden bağlanılırken, CA tarafından verilen bir TLS/SSL sertifika adının, sunucunun ana bilgisayar adıyla eşleşmesi gerekip gerekmediğini belirtir. Varsayılan değer false'tur.  | No |
| allowSelfSignedServerCert | Sunucudan kendinden imzalı sertifikalara izin verilip verilmeyeceğini belirtir. Varsayılan değer false'tur.  | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |
| storageReference | Eşleme veri akışındaki hazırlama verileri için kullanılan depolama hesabının bağlı hizmetine bir başvuru. Bu yalnızca, eşleme veri akışında Hive bağlantılı hizmeti kullanılırken gereklidir | No |

**Örnek:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Hive veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Hive 'den veri kopyalamak için, veri kümesinin Type özelliğini **Hiveobject** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **Hiveobject** olarak ayarlanmalıdır | Yes |
| schema | Şemanın adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablo | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Şema bölümü dahil olmak üzere tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve kullanın `table` . | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Hive kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="hivesource-as-source"></a>Kaynak olarak HiveSource

Hive 'den veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Hivesource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Hivesource** olarak ayarlanmalıdır | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM MyTable"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Hive Bağlayıcısı, veri akışlarında eşleşen bir [satır içi veri kümesi](data-flow-source.md#inline-datasets) kaynağı olarak desteklenir. Bir sorgu kullanarak veya doğrudan HDInsight 'taki Hive tablosundan okuyabilirsiniz. Hive verileri, bir veri akışının parçası olarak dönüştürülmeden önce, bir depolama hesabında, Parquet dosyaları olarak hazırlanır. 

### <a name="source-properties"></a>Kaynak özellikleri

Aşağıdaki tabloda, bir Hive kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Depolama | Deponun olması gerekir `hive` | evet |  `hive` | mağaza | 
| Biçimlendir | Bir tablo veya sorgudan okuma yapıp etmeksizin | evet | `table` veya `query` | biçim |
| Şema adı | Bir tablodan okurken, kaynak tablonun şeması |  Evet, biçim ise `table` | Dize | schemaName |
| Tablo adı | Bir tablodan okurken tablo adı |   Evet, biçim ise `table` | Dize | tableName |
| Sorgu | Biçim ise `query` , Hive bağlı hizmetindeki kaynak sorgusu | Evet, biçim ise `query` | Dize | sorgu |
| Hazırlanmış | Hive tablosu her zaman hazırlanacaktır. | evet | `true` | hazırlanmış |
| Depolama kapsayıcısı | Hive 'dan okumadan veya Hive 'e yazmadan önce verileri hazırlamak için kullanılan depolama kapsayıcısı. Hive kümesinin bu kapsayıcıya erişimi olması gerekir. | evet | Dize | storageContainer |
| Hazırlama veritabanı | Bağlı hizmette belirtilen kullanıcı hesabının erişimi olduğu şema/veritabanı. Hazırlama sırasında dış tablolar oluşturmak için kullanılır ve daha sonra bırakılır | hayır | `true` veya `false` | stagingDatabaseName |
| SQL betikleri öncesi | Verileri okumadan önce Hive tablosunda çalıştırılacak SQL kodu | hayır | Dize | preSQLs |

#### <a name="source-example"></a>Kaynak örneği

Hive kaynak yapılandırmasına bir örnek aşağıda verilmiştir:

![Hive kaynağı örneği](media/data-flow/hive-source.png "[Hive kaynak örneği")

Bu ayarlar aşağıdaki veri akışı betiğine çeviri yapar:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Bilinen sınırlamalar

* Diziler, Haritalar, yapılar ve birleşimler gibi karmaşık türler okuma için desteklenmez. 
* Hive Bağlayıcısı yalnızca sürüm 4,0 veya üzeri Azure HDInsight 'ta Hive tablolarını destekler (Apache Hive 3.1.0)

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
