---
title: Azure Data Factory kullanarak HBase 'den veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak HBase 'den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 02f4b88b1dab99b3b052f59f91f7869d8aedc77f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388371"
---
# <a name="copy-data-from-hbase-using-azure-data-factory"></a>Azure Data Factory kullanarak HBase 'den veri kopyalama 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, verileri HBase 'den kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu HBase Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Verileri HBase 'den desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar, bu nedenle bu bağlayıcıyı kullanarak herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, HBase bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılar sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

HBase bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **HBase** olarak ayarlanmalıdır | Yes |
| konak | HBase sunucusunun IP adresi veya ana bilgisayar adı. Yani.  `[clustername].azurehdinsight.net`, `192.168.222.160` )  | Yes |
| port | HBase örneğinin istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. Varsayılan değer 9090 ' dir. Azure Hdınsights 'e bağlanıyorsanız, bağlantı noktasını 443 olarak belirtin. | No |
| httpPath | HBase sunucusuna karşılık gelen kısmi URL (örn. `/hbaserest0` hdınsights kümesi kullanılırken). | No |
| authenticationType | HBase sunucusuna bağlanmak için kullanılacak kimlik doğrulama mekanizması. <br/>İzin verilen değerler: **anonim**, **temel** | Yes |
| username | HBase örneğine bağlanmak için kullanılan Kullanıcı adı.  | No |
| password | Kullanıcı adına karşılık gelen parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| enableSsl | Sunucu bağlantılarının TLS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer false'tur.  | No |
| trustedCertPath | TLS üzerinden bağlanılırken sunucuyu doğrulamak için güvenilir CA sertifikaları içeren. ped dosyasının tam yolu. Bu özellik yalnızca, şirket içinde barındırılan IR 'de TLS kullanılırken ayarlanabilir. Varsayılan değer, IR ile birlikte yüklenen CAcert. Pez dosyasıdır.  | No |
| Allowwhostnamecnuyuşmazlığını | TLS üzerinden bağlanılırken, CA tarafından verilen bir TLS/SSL sertifika adının, sunucunun ana bilgisayar adıyla eşleşmesi gerekip gerekmediğini belirtir. Varsayılan değer false'tur.  | No |
| allowSelfSignedServerCert | Sunucudan kendinden imzalı sertifikalara izin verilip verilmeyeceğini belirtir. Varsayılan değer false'tur.  | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |No |

>[!NOTE]
>Kümeniz, HDInsight gibi yapışkan oturumu desteklemiyorsa, http yolu ayarının sonunda doğrudan düğüm dizini ekleyin, örneğin `/hbaserest0` yerine belirtin `/hbaserest` .

**Hdınsights HBase için örnek:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<cluster name>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbaserest0",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "enableSsl" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Genel HBase için örnek:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<host e.g. 192.168.222.160>",
            "port" : "<port>",
            "httpPath" : "<e.g. /gateway/sandbox/hbase/version>",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "enableSsl" : true,
            "trustedCertPath" : "<trustedCertPath>",
            "allowHostNameCNMismatch" : true,
            "allowSelfSignedServerCert" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, HBase veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Verileri HBase 'den kopyalamak için, veri kümesinin Type özelliğini **Hbaseobject** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği: **Hbaseobject** olarak ayarlanmalıdır | Yes |
| tableName | Tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "HBaseDataset",
    "properties": {
        "type": "HBaseObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<HBase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, HBase kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="hbasesource-as-source"></a>Kaynak olarak HBaseSource

HBase 'den veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **Hbasesource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Hbasesource** olarak ayarlanmalıdır | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM MyTable"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromHBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HBase input dataset name>",
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
                "type": "HBaseSource",
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
