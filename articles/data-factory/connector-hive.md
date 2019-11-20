---
title: Azure Data Factory kullanarak Hive 'den veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak Hive 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 6bc644c960fdfa24c7ae7e190d5a110cdba41f9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680838"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Azure Data Factory kullanarak Hive 'den veri kopyalama 

Bu makalede, Hive 'den veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Hive Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Verileri Hive 'dan desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar, bu nedenle bu bağlayıcıyı kullanarak herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Hive bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Hive bağlantılı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **Hive** olarak ayarlanmalıdır | Evet |
| Konağının | Birden çok konak için '; ' ile ayrılmış Hive sunucusunun IP adresi veya ana bilgisayar adı (yalnızca serviceDiscoveryMode etkinleştirildiğinde).  | Evet |
| port | Hive sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. Azure Hdınsights 'e bağlanıyorsanız, bağlantı noktasını 443 olarak belirtin. | Evet |
| serverType | Hive sunucusunun türü. <br/>İzin verilen değerler: **HiveServer1**, **HiveServer2**, **hivethriftserver** | Hayır |
| thriftTransportProtocol | Thrift katmanında kullanılacak Aktarım Protokolü. <br/>İzin verilen değerler şunlardır: **binary**, **SASL**, **http** | Hayır |
| authenticationType | Hive sunucusuna erişmek için kullanılan kimlik doğrulama yöntemi. <br/>İzin verilen değerler: **anonim**, **Kullanıcı adı**, **userNameAndPassWord**, **WindowsAzureHDInsightService** | Evet |
| serviceDiscoveryMode | ZooKeeper hizmetinin kullanılması gerektiğini belirtmek için true, false değil.  | Hayır |
| zooKeeperNameSpace | Hive sunucu 2 düğümlerinin eklendiği ZooKeeper üzerindeki ad alanı.  | Hayır |
| useNativeQuery | Sürücünün yerel HiveQL sorguları kullanıp kullanmadığını veya onları HiveQL 'teki eşdeğer bir biçime dönüştürmeyeceğini belirtir.  | Hayır |
| kullanıcı adı | Hive sunucusuna erişmek için kullandığınız Kullanıcı adı.  | Hayır |
| password | Kullanıcıya karşılık gelen parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Hayır |
| httpPath | Hive sunucusuna karşılık gelen kısmi URL.  | Hayır |
| enableSsl | Sunucu bağlantılarının SSL kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer false'tur.  | Hayır |
| trustedCertPath | SSL üzerinden bağlanılırken sunucuyu doğrulamak için güvenilir CA sertifikaları içeren. ped dosyasının tam yolu. Bu özellik yalnızca, şirket içinde barındırılan IR üzerinde SSL kullanılırken ayarlanabilir. Varsayılan değer, IR ile birlikte yüklenen CAcert. Pez dosyasıdır.  | Hayır |
| useSystemTrustStore | Sistem güven deposundan veya belirtilen ped dosyasından bir CA sertifikası kullanılıp kullanılmayacağını belirtir. Varsayılan değer false'tur.  | Hayır |
| Allowwhostnamecnuyuşmazlığını | SSL üzerinden bağlanırken, CA tarafından verilen bir SSL sertifikası adının, sunucunun ana bilgisayar adıyla eşleşmesi gerekip gerekmediğini belirtir. Varsayılan değer false'tur.  | Hayır |
| allowSelfSignedServerCert | Sunucudan kendinden imzalı sertifikalara izin verilip verilmeyeceğini belirtir. Varsayılan değer false'tur.  | Hayır |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

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

Hive 'den veri kopyalamak için, veri kümesinin Type özelliğini **Hiveobject**olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **Hiveobject** olarak ayarlanmalıdır | Evet |
| manızı | Şemanın adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablosundan | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Şema bölümü dahil olmak üzere tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve `table`kullanın. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

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

Hive 'den veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Hivesource**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **Hivesource** olarak ayarlanmalıdır | Evet |
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

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
