---
title: Azure Data Factory kullanarak Xero 'tan veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak Xero 'tan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: d52e536170c649cbc84b6c6dce92afb76ffe3125
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680015"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Azure Data Factory kullanarak Xero 'tan veri kopyalama

Bu makalede, Xero ' den veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Xero Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Xero 'ten desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Xero Bağlayıcısı şunları destekler:

- Xero [özel uygulama](https://developer.xero.com/documentation/getting-started/api-application-types) ancak ortak uygulama değil.
- Tüm Xero tabloları (API uç noktaları), "raporlar" hariç. 

Azure Data Factory, bağlantıyı etkinleştirmek için yerleşik bir sürücü sağlar, bu nedenle bu bağlayıcıyı kullanarak herhangi bir sürücüyü el ile yüklemeniz gerekmez.

## <a name="getting-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde Xero bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler Xero bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği: **Xero** olarak ayarlanmalıdır | Evet |
| Konağının | Xero sunucusunun uç noktası (`api.xero.com`).  | Evet |
| consumerKey | Xero uygulamasıyla ilişkili tüketici anahtarı. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Evet |
| privateKey | Xero özel uygulamanız için oluşturulan. ped dosyasındaki özel anahtar, bkz. [ortak/özel anahtar çifti oluşturma](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). `openssl genrsa -out privatekey.pem 512`kullanarak **PrivateKey. pek numbits of 512 ile oluşturma** 1024 desteklenmiyor. . Ped dosyasındaki, Unix satır sonları (\n) dahil tüm metni ekleyin, aşağıdaki örneğe bakın.<br/><br/>Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Evet |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true 'dur.  | Hayır |
| Usehostdoğrulaması | SSL üzerinden bağlanırken sunucunun ana bilgisayar adıyla eşleşmesi için Sunucu sertifikasında ana bilgisayar adının gerekli olup olmadığını belirtir. Varsayılan değer true 'dur.  | Hayır |
| Usepeerdoğrulaması | SSL üzerinden bağlanırken sunucunun kimliğini doğrulayıp doğrulamayamayacağını belirtir. Varsayılan değer true 'dur.  | Hayır |

**Örnek:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Örnek özel anahtar değeri:**

. Ped dosyasındaki, Unix satır sonları (\n) dahil tüm metni ekleyin.

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Xero veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Xero öğesinden veri kopyalamak için, veri kümesinin Type özelliğini **XeroObject**olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in Type özelliği: **XeroObject** olarak ayarlanmalıdır | Evet |
| tableName | Tablonun adı. | Hayır (etkinlik kaynağı içinde "sorgu" belirtilmişse) |

**Örnek**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm Xero kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="xero-as-source"></a>Kaynak olarak Xero

Xero adresinden veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **XeroSource**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği: **XeroSource** olarak ayarlanmalıdır | Evet |
| sorgu | Verileri okumak için özel SQL sorgusunu kullanın. Örneğin: `"SELECT * FROM Contacts"`. | Hayır (veri kümesinde "tableName" belirtilmişse) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Xero sorgusunu belirtirken aşağıdakileri aklınızda edin:

- Karmaşık öğeler içeren tablolar birden çok tabloya bölünecektir. Örneğin, banka işlemleri "LineItems" karmaşık bir veri yapısına sahiptir, bu nedenle banka işleminin verileri tablo `Bank_Transaction` ve `Bank_Transaction_Line_Items`eşlenir ve bu sayede birlikte bağlamak üzere yabancı anahtar olarak `Bank_Transaction_ID`.

- Xero verileri iki şema aracılığıyla kullanılabilir: `Minimal` (varsayılan) ve `Complete`. Tüm şema, istenen sorguyu yapmadan önce ek veriler (ör. ID sütunu) gerektiren önkoşul çağrı tabloları içerir.

Aşağıdaki tablolar, en küçük ve tamamen şema ile aynı bilgilere sahiptir. API çağrılarının sayısını azaltmak için en az şemayı kullanın (varsayılan).

- Bank_Transactions
- Contact_Groups 
- Kişiler 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Faturalar 
- Invoices_Credit_Notes
- Invoices_ Peşinatları 
- Invoices_Overpayments 
- Manual_Journals 
- Fazla ödemeler 
- Overpayments_Allocations 
- Peşinatları 
- Prepayments_Allocations 
- Lü 
- Receipt_Validation_Errors 
- Tracking_Categories

Aşağıdaki tablolar yalnızca tüm şemayla sorgulanabilir:

- Tamamlanmış. Bank_Transaction_Line_Items 
- Tamamlanmış. Bank_Transaction_Line_Item_Tracking 
- Tamamlanmış. Contact_Group_Contacts 
- Tamamlanmış. Contacts_Contact_ kişileri 
- Tamamlanmış. Credit_Note_Line_Items 
- Tamamlanmış. Credit_Notes_Line_Items_Tracking 
- Tam. Expense_Claim_ ödemeleri 
- Tamamlanmış. Expense_Claim_Receipts 
- Tamamlanmış. Invoice_Line_Items 
- Tamamlanmış. Invoices_Line_Items_Tracking
- Tamamlanmış. Manual_Journal_Lines 
- Tamamlanmış. Manual_Journal_Line_Tracking 
- Tamamlanmış. Overpayment_Line_Items 
- Tamamlanmış. Overpayment_Line_Items_Tracking 
- Tamamlanmış. Prepayment_Line_Items 
- Tamamlanmış. Prepayment_Line_Item_Tracking 
- Tamamlanmış. Receipt_Line_Items 
- Tamamlanmış. Receipt_Line_Item_Tracking 
- Tamamlanmış. Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliği tarafından desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
