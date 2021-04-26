---
title: Azure Data Factory kullanarak verileri Presto 'dan kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak, Presto 'dan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 33e521d418c219be8eb85b79a0e07d999edb1b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374278"
---
# <a name="copy-data-from-presto-using-azure-data-factory"></a>Azure Data Factory kullanarak verileri Presto 'dan kopyalama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, verileri Presto 'dan kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Presto Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Verileri, Presto 'dan desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar, bu nedenle bu bağlayıcıyı kullanarak herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Connector 'a özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler, Presto Linked Service için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği şu şekilde ayarlanmalıdır: **Presto** | Yes |
| konak | Presto Server 'ın IP adresi veya ana bilgisayar adı. (örn. 192.168.222.160)  | Yes |
| serverVersion | Presto Server sürümü. (örn. 0,148-t)  | Yes |
| kataloglarını | Sunucuya yönelik tüm istekler için Katalog bağlamı.  | Yes |
| port | Presto Server 'ın istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. Varsayılan değer 8080 ' dir.  | No |
| authenticationType | Presto sunucusuna bağlanmak için kullanılan kimlik doğrulama mekanizması. <br/>İzin verilen değerler: **anonim**, **LDAP** | Yes |
| username | Presto sunucusuna bağlanmak için kullanılan Kullanıcı adı.  | No |
| password | Kullanıcı adına karşılık gelen parola. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| enableSsl | Sunucu bağlantılarının TLS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer false'tur.  | No |
| trustedCertPath | TLS üzerinden bağlanılırken sunucuyu doğrulamak için güvenilir CA sertifikaları içeren. ped dosyasının tam yolu. Bu özellik yalnızca, şirket içinde barındırılan IR 'de TLS kullanılırken ayarlanabilir. Varsayılan değer, IR ile birlikte yüklenen CAcert. Pez dosyasıdır.  | No |
| useSystemTrustStore | Sistem güven deposundan veya belirtilen ped dosyasından bir CA sertifikası kullanılıp kullanılmayacağını belirtir. Varsayılan değer false'tur.  | No |
| Allowwhostnamecnuyuşmazlığını | TLS üzerinden bağlanılırken, CA tarafından verilen bir TLS/SSL sertifika adının, sunucunun ana bilgisayar adıyla eşleşmesi gerekip gerekmediğini belirtir. Varsayılan değer false'tur.  | No |
| allowSelfSignedServerCert | Sunucudan kendinden imzalı sertifikalara izin verilip verilmeyeceğini belirtir. Varsayılan değer false'tur.  | No |
| Saat dilimi | Bağlantı tarafından kullanılan yerel saat dilimi. Bu seçenek için geçerli değerler, ıANA saat dilimi veritabanında belirtilmiştir. Varsayılan değer sistem saati bölgesidir.  | No |

**Örnek:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Presto DataSet tarafından desteklenen özelliklerin bir listesini sağlar.

Verileri Presto 'dan kopyalamak için DataSet 'in Type özelliğini **Prestoobject** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **Prestoobject** olarak ayarlanmalıdır | Yes |
| schema | Şemanın adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tablo | Tablonun adı. |Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse)  |
| tableName | Şemanın bulunduğu tablonun adı. Bu özellik geriye dönük uyumluluk için desteklenir. `schema` `table` Yeni iş yükü için ve kullanın. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Presto source tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="presto-as-source"></a>Kaynak olarak Presto

Verileri Presto 'dan kopyalamak için kopyalama etkinliğindeki kaynak türünü **Prestosource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Prestosource** olarak ayarlanmalıdır | Yes |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM MyTable"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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
