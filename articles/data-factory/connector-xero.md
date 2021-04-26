---
title: Azure Data Factory kullanarak Xero 'tan veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak Xero 'tan desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: jingwang
ms.openlocfilehash: d795f8355943032751b911423b8aaa93b2df3206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100366917"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Azure Data Factory kullanarak Xero 'tan veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Xero ' den veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Xero Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Xero 'ten desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak/havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Xero Bağlayıcısı şunları destekler:

- OAuth 2,0 ve OAuth 1,0 kimlik doğrulaması. OAuth 1,0 için bağlayıcı Xero [özel uygulamasını](https://developer.xero.com/documentation/getting-started/getting-started-guide) destekler, ancak ortak uygulamayı desteklememektedir.
- Tüm Xero tabloları (API uç noktaları), "raporlar" hariç.

## <a name="getting-started"></a>Kullanmaya başlama

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde Xero bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler Xero bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Xero** olarak ayarlanmalıdır | Yes |
| connectionProperties | Xero 'e nasıl bağlanılacağını tanımlayan bir özellik grubu. | Yes |
| ***Altında `connectionProperties` :*** | | |
| konak | Xero sunucusunun uç noktası ( `api.xero.com` ).  | Yes |
| authenticationType | İzin verilen değerler `OAuth_2.0` ve ' dir `OAuth_1.0` . | Yes |
| consumerKey | OAuth 2,0 için, Xero uygulamanız için **ISTEMCI kimliğini** belirtin.<br>OAuth 1,0 için, Xero uygulamasıyla ilişkili tüketici anahtarını belirtin.<br>Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| privateKey | OAuth 2,0 için, Xero uygulamanız için **istemci parolasını** belirtin.<br>OAuth 1,0 için, Xero özel uygulamanız için oluşturulan. ped dosyasındaki özel anahtarı belirtin, bkz. [ortak/özel anahtar çifti oluşturma](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key). **Numbits/512 kullanılarak PrivateKey. pek oluşturmak** için `openssl genrsa -out privatekey.pem 512` , 1024 desteklenmez. . Ped dosyasındaki, Unix satır sonları (\n) dahil tüm metni ekleyin, aşağıdaki örneğe bakın.<br/><br>Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| Değerine | Xero uygulamanızla ilişkili kiracı KIMLIĞI. OAuth 2,0 kimlik doğrulaması için geçerlidir.<br>[Erişim yetkiniz olan kiracıları denetlemek için](https://developer.xero.com/documentation/oauth2/auth-flow)Kiracı kimliğini nasıl alabileceğinizi öğrenin. | OAuth 2,0 kimlik doğrulaması için Evet |
| refreshToken | OAuth 2,0 kimlik doğrulaması için geçerlidir.<br/>OAuth 2,0 yenileme belirteci, Xero uygulamasıyla ilişkilendirilir ve erişim belirtecini yenilemek için kullanılır; erişim belirtecinin süresi 30 dakika sonra dolar. Xero yetkilendirme akışının nasıl çalıştığını ve [Bu makaleden](https://developer.xero.com/documentation/oauth2/auth-flow)yenileme belirtecini nasıl alabileceğinizi öğrenin. Yenileme belirteci almak için [offline_access kapsamını](https://developer.xero.com/documentation/oauth2/scopes)istemeniz gerekir. <br/>**Kısıtlamayı öğrenin**: Note Xero, erişim belirteci yenilemesi için kullanıldıktan sonra yenileme belirtecini sıfırlar. Çalışan iş yükü için, her bir kopyalama etkinliği çalıştırılmadan önce, ADF 'nin kullanması için geçerli bir yenileme belirteci ayarlamanız gerekir.<br/>Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | OAuth 2,0 kimlik doğrulaması için Evet |
| useEncryptedEndpoints | Veri kaynağı uç noktalarının HTTPS kullanılarak şifrelenip şifrelenmediğini belirtir. Varsayılan değer true şeklindedir.  | No |
| Usehostdoğrulaması | TLS üzerinden bağlanırken sunucunun ana bilgisayar adıyla eşleşmesi için Sunucu sertifikasında ana bilgisayar adının gerekli olup olmadığını belirtir. Varsayılan değer true şeklindedir.  | No |
| Usepeerdoğrulaması | TLS üzerinden bağlanılırken sunucu kimliğinin doğrulanıp doğrulanmayacağını belirtir. Varsayılan değer true şeklindedir.  | No |

**Örnek: OAuth 2,0 kimlik doğrulaması**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<client ID>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

**Örnek: OAuth 1,0 kimlik doğrulaması**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
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

Xero öğesinden veri kopyalamak için, veri kümesinin Type özelliğini **XeroObject** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **XeroObject** olarak ayarlanmalıdır | Yes |
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

Xero adresinden veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **XeroSource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **XeroSource** olarak ayarlanmalıdır | Yes |
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

- Karmaşık öğeler içeren tablolar birden çok tabloya bölünecektir. Örneğin, banka işlemleri "LineItems" karmaşık veri yapısına sahiptir, bu nedenle banka işleminin verileri tabloyla eşlenir `Bank_Transaction` ve `Bank_Transaction_Line_Items` `Bank_Transaction_ID` bunları birbirine bağlamak için yabancı anahtar olarak.

- Xero verileri iki şema aracılığıyla kullanılabilir: `Minimal` (varsayılan) ve `Complete` . Tüm şema, istenen sorguyu yapmadan önce ek veriler (ör. ID sütunu) gerektiren önkoşul çağrı tabloları içerir.

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
- Peşinatlar Invoices_ 
- Invoices_Overpayments 
- Manual_Journals 
- Fazla ödemeler 
- Overpayments_Allocations 
- Peşinatları 
- Prepayments_Allocations 
- Makbuzlar 
- Receipt_Validation_Errors 
- Tracking_Categories

Aşağıdaki tablolar yalnızca tüm şemayla sorgulanabilir:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ kişiler 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ ödemeler 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.


## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliği tarafından desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
